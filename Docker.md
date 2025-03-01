**LAB 01** : **Pull the Ubuntu image from Docker hub and launch a web application in the container 
on port no. 8080 and this application should be reachable globally.**

Create a docker instance and connect it to a terminal also add port 8080 to security group.

yum update -y
  
sudo yum install -y yum-utils

sudo yum install docker -y

sudo systemctl start docker

systemctl enable docker 

docker images

docker run -it --name my-container -p 8080:80 ubuntu:latest /bin/bash
 
apt update -y

apt install apache2 -y

cd /var/www/html

echo "...." >index.html

service apache start

**ctrl+p ctrl+q to quit from container**

docker ps -a

curl http://ipaddress

Go and check on browser using public ip
