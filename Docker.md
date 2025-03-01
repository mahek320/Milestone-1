LAB 01: Deploy a Web Application in a Docker Container<br>
This lab demonstrates how to:<br><br>
✅ Pull the Ubuntu image from Docker Hub<br>
✅ Launch a web application in a container on port 8080<br>
✅ Make the application globally accessible<br>

1️⃣ Create a Docker Instance & Configure Security<br>
- Launch a new instance and connect via terminal<br>
- Ensure port 8080 is open in the security group<br>

2️⃣ Install Required Packages<br>
- Run the following commands:<br><br>

```sh
# Update system packages
yum update -y

# Install necessary utilities
sudo yum install -y yum-utils

# Install Docker
sudo yum install docker -y

# Start and enable Docker service
sudo systemctl start docker
sudo systemctl enable docker
```
3️⃣ Pull Ubuntu Image & Run a Container
```sh
# Check available Docker images (optional)
docker images

# Pull and run the Ubuntu container with port mapping
docker run -it --name my-container -p 8080:80 ubuntu:latest /bin/bash
```
4️⃣ Install & Configure Apache Web Server
Inside the running container, execute:

```sh
# Update package list
apt update -y

# Install Apache web server
apt install apache2 -y

# Navigate to the web root directory
cd /var/www/html

# Create a simple index.html file
echo "Welcome to My Web Server!" > index.html

# Start Apache service
service apache2 start
```
5️⃣ Exit Container & Verify Deployment
**Press CTRL + P and CTRL + Q** to detach from the container
Check running containers:
```sh
docker ps -a
```
Test the application:<br>
curl http://<public-ip>:8080<br>
Open http://<public-ip>:8080 in a web browser

**Your web application is now accessible globally!** 

📌 Notes:<br>
Replace <public-ip> with your actual instance public IP<br>
Ensure security group rules allow HTTP (port 8080)
