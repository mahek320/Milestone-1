# 🚀 Ansible-EKS Deployment Guide  

After successfully **pushing the image to ECR**, follow these steps to set up Ansible with EKS.  


1️⃣ Set Up Required Servers<br>
Ansible Server → Used for automation<br>
Kubernetes Server → Hosts the cluster<br>

2️⃣ Install kubectl & eksctl on Kubernetes Server<br>
Ensure kubectl and eksctl are installed, then create an EKS cluster with a node group.<br>

3️⃣ Install Ansible on Ansible Server<br>
```sh
sudo apt install ansible -y
```
4️⃣ Configure Ansible Inventory File<br>
Create an inventory file:
```sh
sudo vim /etc/ansible/hosts
```
If using a custom inventory file, define it in ansible.cfg:<br>
```sh
[defaults]
inventory = /etc/ansible/hosts
```
5️⃣ Add Managed Nodes to Inventory<br>
Specify the private IP addresses of the nodes:<br>
```sh
[servers]
server1 ansible_host=your_server_ip
server2 ansible_host=your_server_ip
```
6️⃣ Setup SSH Keys for Secure Connections
Generate ssh-key : 
```sh
*ssh-keygen*
```
Copy the SSH key to managed nodes (after updating sshd_config settings):<br>
```sh
ssh-copy-id root@your_server_ip
```
Verify connectivity:<br>
```sh
ansible all -m ping
```
7️⃣ Install Kubernetes Python Libraries<br>
On the Kubernetes server, install the required libraries:<br>
```sh
pip install openshift kubernetes
```

8️⃣ Create Ansible Playbook for Kubernetes Deployment<br>
On the Ansible server, create a directory and a playbook (deploy.yml):<br>
```sh
---
- hosts: 172.31.5.42
  gather_facts: no
  tasks:
    - name: Deploy application in Kubernetes
      k8s:
        state: present
        definition:
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: my-app
            namespace: default
            labels:
              app: my-app
          spec:
            replicas: 3
            selector:
              matchLabels:
                app: my-app
            template:
              metadata:
                labels:
                  app: my-app
              spec:
                containers:
                - name: my-app
                  image: 039612874025.dkr.ecr.ap-south-1.amazonaws.com/my-data:latest
                  imagePullPolicy: Always
                  ports:
                  - containerPort: 8080
            strategy:
              type: RollingUpdate
              rollingUpdate:
                maxSurge: 1
                maxUnavailable: 1
    - name: Copy service file to target
      copy:
        src: service.yml
        dest: /tmp/service.yml
 
    - name: Create Kubernetes Service
      command: kubectl apply -f /tmp/service.yml
      register: service_output
```
9️⃣ Create service.yml File<br>
This file defines the Kubernetes Service that exposes the application.<br>

```sh
apiVersion: v1
kind: Service
metadata:
  name: regapp-service
  labels:
    app: my-app
spec:
  selector:
    app: my-app

  ports:
    - port: 8080
      targetPort: 8080

  type: LoadBalancer
```
🔟 Deploy and Verify<br>
Run the Ansible playbook manually:<br>
```sh
ansible-playbook deploy.yml
```
Check if the service is created on the Kubernetes server:<br>
```sh
kubectl get services
```
Retrieve the External IP of the service and use it to host the application.
