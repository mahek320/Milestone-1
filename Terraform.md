## TERRAFORM 



#### Install Terraform

sudo yum install -y yum-utils shadow-utils

sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

sudo yum -y install terraform

#### Version

terraform -v

#### Install aws cli

``` yml

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

```

#### Make directory

mkdir /project

cd /project

#### Vim file 
#### Naming extension for terraform file is tf
#### Create an user for access key and secret key
#### The region should be of the ami created

vim provider.tf

``` tf

provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"     #add the keys
  secret_key = "my-secret-key"
}

```

#### Terminal

l.

yum install tree -y

tree -a  (*To check the generated file*)

terraform init

#### Inside vim file 
#### my ec2 code
#### copy ami from other other resources on a different region
#### Give the name of your instance
#### Name of the key should be of new region 
#### Create new key pair from key pair option

``` tf

resource "aws_instance" "this" {
  ami                     = "ami-0dcc1e21636832c5d"
  instance_type           = "t2.micro"
  key_name                = "terraform-key"      # *Give name of the key pair*
}

```

#### Terraform init

terraform init

terraform validate (*The configuration is valid*)

terraform plan

terraform apply 

#### Connect the new isntance on a new terminal 

#### Security group (SSH, All traffic)

yum install httpd -y

#### Old terminal 
cd /project

aws configure

vim provider.tf (*Backspace the first para*)

terraform destroy

ll
(*After ll you will get a file that wasn't created by you, do cat filename*)
cat terraform.tstate

#### Manage tag new instance

manage tag >> name

terraform fmt

cat provider.tf

terraform apply 

terraform destroy

#### Old terminal 

mkdir /mahek
cd /project
cd provider.tf /mahek
cd /mahek
mv provider.tf my-resource.tf 
vim my-resource.tf

resource "aws_instance" "this" {
  ami                     = "ami-0dcc1e21636832c5d"
  instance_type           = "t2.micro"
  availability_zone       = "us-east-1a"
  key_name                = "terraform-key"      # *Give name of the key pair*
  
}

#### To create an instance from terminal, add security groups, attach abs and volume.
#### Configure aws
#### Create a new key pair in the new region where you want to create a new instance
#### Terraform init, fmt (format), validate, plan, apply

vim security.tf

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

#### Go check on aws ,open your instance > storage > volume > open your attached volume.

#### Create vpc and subnet

vim vpc.tf

``` tf
resource "aws_vpc" "main" {
  cidr_block       = "10.0.0.0/16"
  instance_tenancy = "default"

  tags = {
    Name = "main"
  }
}
resource "aws_subnet" "main" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "Main"
  }
}

```


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

has context menu
 

```









