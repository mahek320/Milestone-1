## TERRAFORM 

**Terraform is a popular infrastructure-as-code tool that allows you to automate the provisioning and management of infrastructure resources.**

**Infrastructure as Code (IaC) is a method of managing and provisioning IT infrastructure using code, rather than manual configuration.**

**It uses configuration files written in the HashiCorp Configuration Language (HCL) to define the desired state of your infrastructure**

**Terraform commands**

**terraform init**  -  It prepares the current working directory for use with Terraform

**terraform fmt** - This helps code readability and ensures all configuration files are formatted the same, no matter which team member is writing the file.

**terraform validate** - By using this command, developers can catch and solve configuration errors before attempting to apply changes to the infrastructure.

**terraform plan** -  Shows changes needed by the current configuration 

**terraform apply** - Implements the changes 



Install Terraform
- sudo yum install -y yum-utils shadow-utils

- sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

- sudo yum -y install terraform

Version
- terraform -v

#### Install aws cli

``` yml

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

```

Make directory

- mkdir /project

- cd /project

**Vim file**

-> Naming extension for terraform file is tf

vim provider.tf

``` tf

provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"     #add the keys
  secret_key = "my-secret-key"
}

```

**Note** : If you are running aws configure, you do not need to paste the above content into your file.

Terminal
- yum install tree -y

- tree -a  (*To check the generated file*)

- terraform init

**Inside provider.tf file using vim** 

To create an instance through the terminal:
- Specify the name of your instance.
- Paste the AMI.
- Enter the instance type you wish to add.
- Enter the key-pair name.
 

``` tf

resource "aws_instance" "this" {
  ami                     = "ami-0dcc1e21636832c5d"
  instance_type           = "t2.micro"
  availability_zone       = "us-east-1a"
  key_name                = "terraform-key"      # *Give name of the key pair*
  
}
```
- terraform init

- terraform fmt

- terraform validate (*The configuration is valid*)

- terraform plan

- terraform apply 

Check the dashboard to see if your instance has been created, and then connect to it using the new terminal.

To delete the terraform created by you
- terraform destroy

**Inorder to add security group in the instance created by you (SSH, All traffic)**
- yum install httpd -y


vim provider.tf

``` tf

provider "aws" {
  region = "ap-south-1"
}
 
#security group
resource "aws_security_group" "webserver-sg" {
  name        = "webserver-sg"
  description = "allow ssh and http"
 
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
 
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
 
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
 
}
 
#create instance
resource "aws_instance" "EC2-instance-1" {
  ami               = "ami-08718895af4dfa033"
  availability_zone = "ap-south-1a"
  instance_type     = "t2.micro"
  security_groups   = ["${aws_security_group.webserver-sg.name}"]
  key_name          = "trf-kp"
  tags = {
    Name = "EC2-instance-1"
  }
}

```
**To add a block storage and attach volume**
vim provider.tf 

```tf
#create block storage
resource "aws_ebs_volume" "data_vol" {
  availability_zone = "ap-south-1a"
  size              = 5
}
 
resource "aws_volume_attachment" "EC2-instance-1-vol" {
  device_name = "/dev/sdc"
  volume_id   = aws_ebs_volume.data_vol.id
  instance_id = aws_instance.EC2-instance-1.id
}

```

terraform init 

terraform fmt 

terraform validate 

terraform plan

terraform apply 

Inorder to check the changess go to your aws dashboard > open your instance > storage > volume > open your attached volume.

**Using Terraform, create a custom VPC with two subnets: one public and one private. Then, create two instances within these subnets: a web server and a database server.**

```tf

resource "aws_vpc" "tera-vpc" {

  cidr_block = "10.0.0.0/16"
 
  tags = {

    Name = "tera-vpc"

  }
 
}
 
resource "aws_subnet" "public-subnet" {

  vpc_id            = aws_vpc.tera-vpc.id

  cidr_block        = "10.0.0.0/24"

  availability_zone = "us-east-1a"
 
  tags = {

    Name = "public-subnet"

  }

}
 
resource "aws_subnet" "private-subnet" {

  vpc_id     = aws_vpc.tera-vpc.id

  cidr_block = "10.0.1.0/24"

  availability_zone = "us-east-1b"
 
  tags = {

    Name = "private-subnet"

  }

}
 
resource "aws_internet_gateway" "my-internet-gateway" {

  vpc_id     = aws_vpc.tera-vpc.id
 
  tags = {

    Name = "my-igw"

  }

}
 
 
resource "aws_route_table" "public-rout" {

  vpc_id = aws_vpc.tera-vpc.id
 
  route {

    cidr_block = "0.0.0.0/0"

    gateway_id = aws_internet_gateway.my-internet-gateway.id

  }
 
  tags = {

    Name = "public-route-table"

  }

}
 
resource "aws_route_table_association" "association" {

  subnet_id      = aws_subnet.public-subnet.id

  route_table_id = aws_route_table.public-rout.id

}
 
resource "aws_eip" "nat" {

  domain = "vpc"
 
  tags = {

    Name = "nat-eip"

  }

}

resource "aws_nat_gateway" "NAT-gw" {

  allocation_id = aws_eip.nat.id

  connectivity_type = "public"

  subnet_id     = aws_subnet.public-subnet.id
 
  tags = {

    Name = "NAT-gw"

  }

}
 
resource "aws_route_table" "private-rout" {

  vpc_id = aws_vpc.tera-vpc.id
 
  route {

    cidr_block = "0.0.0.0/0"

    nat_gateway_id = aws_nat_gateway.NAT-gw.id

  }
 
  tags = {

    Name = "private-route-table"

  }

}
 
resource "aws_route_table_association" "association-1" {

  subnet_id      = aws_subnet.private-subnet.id

  route_table_id = aws_route_table.private-rout.id

}
 
 
resource "aws_security_group" "my-vpc-sg" {

  name        = "my-vpc-sg"

  description = "Allow HTTP and SSH"

  vpc_id = aws_vpc.tera-vpc.id

  ingress {

    from_port   = 22

    to_port     = 22

    protocol    = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }
 
  ingress {

    from_port   = 80

    to_port     = 80

    protocol    = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }
 
  egress {

    from_port   = 0

    to_port     = 0

    protocol    = "-1"

    cidr_blocks = ["0.0.0.0/0"]

  }

}
 
 
resource "aws_instance" "my-vpc-instance" {

  ami               = "ami-0ebfd941bbafe70c6"

  instance_type     = "t2.micro"

  subnet_id         = aws_subnet.public-subnet.id

  key_name          = "terraform-key1"

  associate_public_ip_address = true

  security_groups   = [aws_security_group.my-vpc-sg.id]
 
  tags = {

    Name = "vpc-public-instance"

  }

}
 
resource "aws_instance" "my-vpc-private-instance" {

  ami               = "ami-0ebfd941bbafe70c6"

  instance_type     = "t2.micro"

  subnet_id         = aws_subnet.private-subnet.id

  key_name          = "terraform-key1"

  associate_public_ip_address = false

  security_groups   = [aws_security_group.my-vpc-sg.id]
 
  tags = {

    Name = "vpc-private-instance"

  }

}

```









