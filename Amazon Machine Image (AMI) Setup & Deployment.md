
# 🚀 Amazon Machine Image (AMI) Setup & Deployment Guide

## **1️⃣ Create an EC2 Instance**
1. Launch a new **Amazon EC2 Instance**.
2. Add a **security group** allowing HTTP (`port 80`).

---

## **2️⃣ Install and Configure Apache Web Server**
### Open Terminal and Run:
```sh
yum update -y
yum install httpd -y
rpmquery httpd
cd /var/www/html/
echo "This is my web server" > index.html
ll
cat index.html
```
**Start and Enable Apache Service:**
```sh
systemctl start httpd
systemctl status httpd
systemctl enable httpd
```
Verify Web Server:
```sh
curl http://localhost
ip a s  # Get the IP Address
curl http://<your-ip-address>
```
3️⃣ Install Additional Utilities
```sh
yum install vsftpd -y
yum install cifs-utils -y
yum install nfs-utils -y
yum install tree -y
```
4️⃣ Create Dummy Files for Testing
```sh
cd /tmp/
touch glenn.txt{1..100}
ll
cd
```
5️⃣ Create an AMI (Amazon Machine Image)<br>
Right-click on the instance → Images & Templates → Click Create Image.
Click on AMI (left panel) → Select the newly created image → Click Launch Instance.<br>
Open the terminal in the new AMI instance.<br>
Change to ec2-user and verify the HTTPD installation:<br>
```sh
sudo su - ec2-user
rpmquery httpd
```
6️⃣ Copy AMI to Another Region<br>
Go to the AMI List → Select the AMI.
Click on Actions → Copy AMI.
Change the region → Click Copy.<br>
Navigate to the new region → Open AMI and check its status.<br>
Once the AMI is ready, launch a new instance in the new region.<br><br>
7️⃣ Test the Web Server<br>
Copy the Public IP of the new instance.<br>
Paste it in your browser to verify the Apache server is running.<br>


