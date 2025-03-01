# EKS-Cluster

A Kubernetes cluster consists of a **control plane** plus a set of **worker machines** (nodes) that run containerized applications. Every cluster needs at least one worker node to run **Pods**.

## Namespaces
Namespaces are a way to divide cluster resources between multiple users. Kubernetes has four default namespaces:
- default
- kube-node-lease
- kube-public
- kube-system

## Pods
A **pod** is the smallest deployable unit in Kubernetes. It can run a single container or multiple tightly coupled containers that share resources.

## ReplicaSet
A **ReplicaSet** ensures a specified number of identical pods are running at any given time.

In this repository, we have deployed a web application in a Kubernetes pod and created a **ReplicaSet**. If the load increases, we perform **autoscaling** and **manual scaling** to adjust the number of replicas. We have also implemented **rolling updates**.

---

## Creating an EC2 Instance for EKS
1. **Create EC2 instance** with:
   - **Name:** EKS-inst
   - **AMI Type:** Ubuntu
   - **Instance Type:** t2.micro
   - **Storage:** 1x10 GiB
   - **Security Group:** Allow all traffic
   - **IAM Role:** Full access to AmazonEC2ContainerServiceFullAccess, AmazonEKSClusterPolicy, and IAMFullAccess

### Install AWS CLI
```sh
apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### Configure AWS CLI
```sh
aws configure
```
- Enter region (e.g., `us-east-1`)
- Enter default output format as `table`

### Install EKS Tool
```sh
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

### Install `kubectl`
```sh
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

## Create EKS Cluster
```sh
eksctl create cluster --name my-cluster --region us-west-1 --version 1.29 --vpc-public-subnets subnet-ExampleID1,subnet-ExampleID2 --without-nodegroup
```
(*Replace `ExampleID1` and `ExampleID2` with actual subnet IDs from the VPC dashboard.*)

### Create Node Group
```sh
ssh-keygen
eksctl create nodegroup \
--cluster my-cluster \
--region us-east-2 \
--name my-node-group \
--node-ami-family Ubuntu2004 \
--node-type t2.small \
--subnet-ids subnet-XXXXX1,subnet-XXXXX2 \
--nodes 3 \
--nodes-min 2 \
--nodes-max 4 \
--ssh-access \
--ssh-public-key /root/.ssh/id_rsa.pub
```
(*Replace `subnet-XXXXX1` and `subnet-XXXXX2` with actual subnet IDs.*)

### Delete Cluster
```sh
eksctl delete cluster --name my-cluster
```

---

## Create a ReplicaSet
### Create `replicaset.yml`
```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  replicas: 4
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: apache-app
        image: nginx
```
Apply the ReplicaSet:
```sh
kubectl apply -f replicaset.yml
kubectl get pods
```

**Describe a pod:**
```sh
kubectl describe pod <pod-name>
```

---

## Scaling Pods
### Autoscaling (Horizontal Scaling)
```sh
kubectl autoscale rs frontend --max=10 --min=3
```

### Manual Scaling (Vertical Scaling)
```sh
kubectl scale --replicas=9 -f replicaset.yml
kubectl get rs
```

---

## Deployments
### Create `mahek-deployment.yml`
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mahek-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
Apply Deployment:
```sh
kubectl apply -f mahek-deployment.yml
kubectl get pods
kubectl get deployment
```

### Rolling Updates
```sh
kubectl set image deployment.v1.apps/mahek-deployment nginx=nginx:1.16.1
kubectl describe deployment mahek-deployment
```

---

## Pushing an Image to EKS from Docker
### Create an Instance & Install Docker
```sh
yum install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
yum install docker -y
systemctl start docker
systemctl enable docker
docker info
```

### Create & Push Docker Image
#### Create `Dockerfile`
```Dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y apache2
RUN apt-get install -y tree openssh-server openssh-client
RUN echo "Welcome to DevOps" > /var/www/html/index.html
RUN service apache2 start
```

#### Build & Push Image
```sh
docker build -t lti-devops .
docker tag lti-devops:latest <ECR-URL>/lti-devops:latest
docker push <ECR-URL>/lti-devops:latest
```
(*Replace `<ECR-URL>` with your actual Elastic Container Registry URL.*)

### Deploy Docker Image in EKS
Update `mahek-deployment.yml` with ECR image:
```yml
image: <ECR-URL>/lti-devops:latest
```
Apply Deployment:
```sh
kubectl apply -f mahek-deployment.yml
kubectl get pods
kubectl get deployment
```

---

This completes the **EKS Cluster** setup and deployment! 🚀


  
    
    




   





   
