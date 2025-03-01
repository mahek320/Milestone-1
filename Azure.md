# Azure Setup Guide

## **LAB 01**: VNET, Subnet, Load Balancer, and Apache Setup

1. **Create a VNET and Subnet**<br>
   - Go to **Azure** > **Virtual Network** > **Resource Group** > Create New > Create and Review<br>
   - Navigate to **Resource** > **Subnets** > Add **1 Private** and **1 Public**<br>

2. **Provision Virtual Machines**<br>
   - Go to **Virtual Machines** > Choose Correct Resource Group<br>
   - Set **Name** > **Networking** > **Subnet**<br>
   - Assign **2 Private** and **2 Public** for 4 instances<br>
   - Create > Download **Private Key**<br>
   - Go to Resource > Connect > **Native SSH** > Copy SSH Command<br>

3. **Set Up Apache Server (Terminal 1)**<br>
```sh
apt update -y
apt install apache2
cd /var/www/html
ls
cat > index.html
systemctl start apache2
systemctl status apache2
```

4. **Allow Port Access**<br>
   - Click on **Instance** > **Networking** > **Network Setting** > **Create Port Rule**<br>
   - Do **not** add source port<br>
   - Paste **Public IP** on browser<br>

5. **Create and Configure Load Balancer**<br>
   - Go to **Load Balancer** > **Create**<br>
   - Set **Frontend IP** > Name > Save<br>
   - Create **Backend Pool** > Select IPs<br>
   - Review and Create > View **Health Probes** > Name > Save<br>
   - **Load Balancing Rules** > Add > Port **80** (Backend Port: 80) > Save<br>

6. **Create Storage Account and Upload a File**<br>
   - Search **Storage Accounts** > **Create New** > **Review and Create**<br>
   - Click on **Created Storage** > **Upload a File**<br>
   - Create **New Container** > Upload File<br>

7. **Configure Blob Storage in Terminal**<br>
```sh
cat /etc/*-release
sudo wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
sudo apt-get install blobfuse
sudo mkdir /mnt/resource/blobfusetmp -p
vim fuse_connection.cfg
```

8. **Configure Blob Storage Credentials** (Inside Vim)<br>
```sh
accountName storageb05
accountKey "<access-key-from-security-and-networking>"
containerName newcontainer
authType Key
```

9. **Mount Blob Storage**<br>
```sh
sudo chmod 600 fuse_connection.cfg
sudo mkdir ~/mycontainer
apt-get install libfuse2
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp --config-file=fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
cd mycontainer
ls
echo "This is Mahek Shetty" > index1.html
ls
```

10. **Expose Apache Server on Port 80**<br>
   - Paste **Frontend Public IP** into the browser<br>

---

## **LAB 02**: Create an AKS Cluster via Dashboard and Terminal

### **Azure Dashboard Method**<br>
1. Search **AKS** > Click **Create**<br>
2. Set **Node Pools** > Define **Size** > Add<br>
3. Review and **Create Cluster**<br>

### **Create AKS Cluster via Terminal**<br>
```sh
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
sudo apt-get update
sudo apt-get install azure-cli
az version
az aks install-cli
az login
```

4. **Set Subscription and Get Cluster Credentials**<br>
```sh
az account set --subscription c6a634ad-2fb9-4fb8-ba44-c38b37f19f12
az aks get-credentials --resource-group mahek-network --name mahek-cluster --overwrite-existing
```

_(To get details: Go to your cluster > Properties > Note **Resource Group, Cluster Name, and Subscription ID**)_<br>

5. **Verify Cluster Status**<br>
```sh
kubectl get deployments --all-namespaces=true
kubectl get deployments
kubectl logs -h
kubectl get nodes
```

