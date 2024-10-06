**Pull the Ubuntu image from Docker hub and launch a web application in the container 
on port no. 8080 and this application should be reachable globally.**

yum update -y
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo yum install docker -y
sudo systemctl start docker
systemctl enable docker 
docker images
docker run -it --name my-container -p 8080:80 ubuntu:latest /bin/bash
(add port 8080 to security group )
apt update -y
apt install apache2 -y
 cd /var/www/html
echo "...." >index.html
service apache start
ctrl+p ctrl+q to quit from container.
docker ps -a
curl http://ipaddress
Go and check on browser using public ip
