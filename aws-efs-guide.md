# Setting up NFS on AWS EC2 Instances

## Create EC2 Instances
1. Create **3 instances** with different OS:<br>
   - **Linux**<br>
   - **RedHat**<br>
   - **Ubuntu**<br>
2. Assign **availability zones**:<br>
   - 2 instances in **1a**<br>
   - 1 instance in **1b**<br>
3. **Edit Security Group**:<br>
   - Add **NFS (port 2049)** to inbound rules<br>

---

## Configure Instances<br>
### **Linux Instance**<br>
```sh
cat /etc/os-release
rpmquery nfs-utils
mkdir /nfs-data
```

### **AWS Console Steps**<br>
1. **Go to AWS Console**<br>
2. Navigate to **EFS > New File System**<br>
3. Click **Refresh**<br>
4. Update **Security Group** (replace default with your own security group)<br>
5. **Edit** and **Disable File Protection**<br>
6. **Attach EFS**<br>

### **Mount EFS on Linux**<br>
```sh
sudo mount <EFS-MOUNT-TARGET> /nfs-data/
df -h
cd /nfs-data/
touch glenn.txt{1..10}
ls -l
```

---

### **RedHat Instance**<br>
```sh
cat /etc/os-release
yum install nfs-utils -y
rpmquery nfs-utils
mkdir /efs-data
sudo mount <EFS-MOUNT-TARGET> /nfs-data/
df -h
cd /nfs-data/
touch glenn.txt{1..10}
ls -l
```

---

### **Ubuntu Instance**<br>
```sh
apt update 
apt install nfs-common -y
mkdir /remote-data/
sudo mount <EFS-MOUNT-TARGET> /remote-data/
ls -l
```

---

## EFS Replication to Another Region<br>
### **AWS Console Steps**<br>
1. **Choose a new AWS region**<br>
2. Create a **new instance**<br>
3. Create a **new file system (FS)**<br>
   - **Disable FSP** (File System Protection)<br>
4. **Check Networking & Security Group**<br>
   - Assign **custom security group**<br>
5. Update **Inbound Rules**<br>
   - Add **NFS (port 2049)**<br>

### **Create EFS Replica**<br>
1. **Go to Old Region**<br>
2. Select **Existing EFS** > Click **Create Replica**<br>
3. Choose **Destination Region**<br>
4. Browse **EFS** and **Create Replica**<br>

### **Mount EFS Replica**<br>
```sh
df -h
mkdir /data-mum
sudo mount <EFS-REPLICA-MOUNT-TARGE
