# AWS VPC Setup

## Create a VPC
1. Open AWS VPC.
2. Navigate to **Your VPCs**.
3. Select **VPC only**.
4. Create a new VPC:
   - **Name:** test-vpc
   - **IPv4 CIDR:** 10.0.0.0/16
5. Click **Create VPC**.

## Create an Internet Gateway (IGW)
1. Navigate to **Internet Gateways**.
2. Click **Create Internet Gateway**.
   - **Name:** test-igw
3. Click **Create**.
4. Select the created IGW > **Actions** > **Attach to VPC**.

## Create Subnets
### Public Subnet
1. Navigate to **Subnets**.
2. Click **Create Subnet**.
   - **VPC:** Select **test-vpc**
   - **Name:** public-subnet
   - **Availability Zone:** 1a
   - **CIDR Block:** 10.0.0.0/24
3. Click **Create**.

### Private Subnet
1. Click **Create Subnet**.
   - **Name:** private-subnet
   - **Availability Zone:** 1a
   - **CIDR Block:** 10.0.1.0/24
2. Click **Create**.

## Configure Route Tables
### Public Route Table
1. Navigate to **Route Tables**.
2. Click **Create Route Table**.
   - **Name:** public-rt
   - **VPC:** test-vpc
3. Click **Edit Routes** > **Add Route**:
   - **Destination:** 0.0.0.0/0
   - **Target:** Select **IGW**
4. Click **Save**.
5. Click **Subnet Associations** > **Edit** > Select **public-subnet** > **Save**.

## Create EC2 Instance
1. Navigate to **EC2** > **Create Instance**.
2. Configure **Network Settings**:
   - **Subnet:** public-subnet (Enable Public IP)
   - **Availability Zone:** 1a
   - **Security Group:** Create new.
3. Add **Security Group Rules**:
   - **Inbound Rules:** HTTP (Port 80), ICMP (IPv4), All ICMP.

### Configure Web Server
1. Open Terminal.
2. Run the following commands:
   ```sh
   sudo su -
   yum install httpd -y
   cd /var/www/html
   echo "This is my web server" > index.html
   cd
   systemctl start httpd
   systemctl enable httpd
   ```
3. Check if the public IP shows the output in a browser.

## Create a DB Instance
1. Navigate to **EC2** > **Create Instance**.
2. Configure **Network Settings**:
   - **VPC:** test-vpc
   - **Subnet:** private-subnet
   - **Disable Public IP**
   - **Availability Zone:** 1a
   - **Security Group:** Use same as EC2.
3. Add **Security Group Rules**:
   - **Inbound Rules:** SSH (Port 22), ICMP (IPv4), HTTP (Port 80).

### Configure DB Server
1. Open Terminal.
2. Run the following commands:
   ```sh
   ip a s
   ping <IP Address>
   ll
   ```

## Configure SSH Access
1. Open the DB server instance.
2. Copy the instance key `.pem` file and paste it into a Notepad.
3. Open Terminal and run:
   ```sh
   vim <keypairname>.pem
   ```
4. Paste the content, save, and set permissions:
   ```sh
   chmod 400 <keypairname>.pem
   ll -a
   ```
5. Connect to EC2 instance:
   ```sh
   ssh -i <keypairname>.pem ec2-user@<db-server-ip>
   sudo su -
   ```

## Create a NAT Gateway
1. Navigate to **NAT Gateway**.
2. Click **Create NAT Gateway**.
   - **Name:** test-ngw
   - **Subnet:** public-subnet
   - **Connectivity Type:** Public
   - **Allocate IP**
3. Click **Create**.

## Modify Route Tables for NAT Gateway
1. Navigate to **Route Tables**.
2. Select **public-rt**.
3. Click **Edit Routes** > **Add Route**:
   - **Destination:** 0.0.0.0/0
   - **Target:** NAT Gateway
4. Click **Save**.

## Create Another VPC in Another Region
1. Navigate to **VPC** in another region.
2. Click **Create VPC**.
   - **CIDR Block:** 20.0.0.0/16
3. Click **Create**.

## Create a Peering Connection
1. Navigate to **Peering Connections**.
2. Click **Create Peering Connection**:
   - **Requestor:** Old region VPC
   - **Accepter:** New region VPC
3. Copy the VPC ID of the new region.
4. Click **Create**.
5. In the new region, accept the request.

## Modify Route Tables for Peering
1. Navigate to **Route Tables** in the new region.
2. Select **public-rt** > **Edit Routes** > **Add Route**:
   - **Destination:** Old region CIDR
   - **Target:** Peering Connection
3. Click **Save**.
4. In the old region, repeat the process and add the route for the new region.

## Connect Instances Across Regions
### In Old Region
1. Connect to the public instance:
   ```sh
   ssh-keygen
   cd .ssh
   cat id_rsa.pub
   cat authorized_keys
   ```

### In New Region
1. Connect to the public instance.
2. Copy the `id_rsa.pub` from the old region.
3. Open Terminal and run:
   ```sh
   vim authorized_keys
   ```
4. Paste the key from the old region.
5. Copy `id_rsa.pub` from the new region and add it to `authorized_keys` in the old region.

### Transfer Files Between Regions
#### In Old Region
```sh
cat > white.txt
scp white.txt root@<new-region-ip>:/
```

#### In New Region
```sh
cat > black.txt
scp black.txt root@<old-region-ip>:/
```

