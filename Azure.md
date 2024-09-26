## Azure

Go to azure >> virtual network >> resource group >> create new >> create and review 
go to resource >> subnets >> 1 private and 1 public >> add 
go to virtual machines >> choose correct resource group >> name >> networking >> subnet >> 2 private and 2 public for 4 instances >> create >> download private key >> go to resource >>  connect >> native ssh >> copy ssh and replace key name after i

## Code on terminal 1

 apt install httpd -y
 apt update -y
 apt install apache2
  cd /var/www/html
  ls
  cat > index.html
  systemctl start apache2
  systemctl status apache2

Give port access >> click on instance >> networking >> network setting >> create port rule >> don't add source port >> paste public ip on browser

load balancer >> create >> frontend ip >> name >> save >> backend pool >> select ip >> review + create >> create >> view health probes >> name >> save >> load balancing rules >> add >> port and backend port give 80 >> save >> search storage accounts >> create a new storage account >> review and create >> click on the storage created >> upload a file >> create new container >> upload

## Commands on terminal 1

 cat /etc/*-release
 sudo wget
 https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
 sudo wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
 sudo dpkg -i packages-microsoft-prod.deb
 sudo apt-get update
 sudo apt-get install blobfuse
 sudo mkdir /mnt/resource/blobfusetmp -p
 vim fuse_connection.cfg

## Paste this inside vim with the storage account name, 
accountName storageb05
accountKey "go to storage , under security and networking we will get access keys"
containerName newcontainer
authType Key

## Terminal 
 sudo chmod 600 fuse_connection.cfg
 sudo mkdir ~/mycontainer
 apt-get install libfuse2
 sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
 cd mycontainer
 ls
 echo "This is Mansi from ec2" > index1.html
 ls

 ## To Create AKS on Azure dashboard , go to search and and search AKS >> CREATE >> node pools >> size >> add >> review and create 

 ## Create cluster via terminal 

curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
sudo apt-get update
sudo apt-get install azure-cli
az version
az aks install-cli
az login
az account set --subscription c6a634ad-2fb9-4fb8-ba44-c38b37f19f12 
az aks get-credentials --resource-group mansi-network --name mansi-cluster --overwrite-existing    (Go to your cluster properties and then you will get the anme of the resource group, cluster name and subscription id)
kubectl get deployments --all-namespaces=true
kubectl get deployments
kubectl logs -h
kubectl get nodes

 
