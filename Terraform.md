## Security group - All traffic
## Install Terraform

sudo yum install -y yum-utils shadow-utils

sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

sudo yum -y install terraform

## Version

terraform -v

## Install aws cli

``` yml

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

```

## Make directory

mkdir /project

cd /project

## Vim file 
#### Naming extension for terraform file is tf
#### Create an user for access key and secret key
#### The region should be of the ami created

vim provider.tf

``` tf

provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"
  secret_key = "my-secret-key"
}

```

## Terminal

l.

yum install tree -y

tree -a  (*To check the generated file*)

terraform init

## Inside vim file 
### my ec2 code
### copy ami from other other resources on a different region
### Give the name of your instance
### Name of the key should be of new region 
### Create new key pair from key pair option

``` tf

resource "aws_instance" "this" {
  ami                     = "ami-0dcc1e21636832c5d"
  instance_type           = "t2.micro"
  key_name                = "terraform-key"      # *Give name of the key pair*
}

```

## Terraform init

terraform init

terraform validate (*The configuration is valid*)

terraform plan

terraform apply 

## Connect the new isntance on a new terminal 

## Security group (SSH, All traffic)

yum install httpd -y

## Old terminal 
cd /project

aws configure

vim provider.tf (*Backspace the first para*)

terraform destroy

ll
(*After ll you will get a file that wasn't created by you, do cat filename*)
cat terraform.tstate

# Manage tag new instance

manage tag >> name

terraform fmt

cat provider.tf

terraform apply 

terraform destroy

# Old terminal 

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

vim my-resurce.tf

``` tf

#This is my secuurity code 

resource "aws_security_group" "web-server-sg" {
  name        = "web-server-sg"
  description = "Allow ssh and http inbound traffic and all outbound traffic"
 

  tags = {
    Name = "web-server-sg"
  }
}

resource "aws_vpc_security_group_ingress_rule" "allow_http_ipv4" {
  security_group_id = aws_security_group.allow_tls.id
  cidr_ipv4         = aws_vpc.main.cidr_block
  from_port         = 80
  ip_protocol       = "tcp"
  to_port           = 80
}

resource "aws_vpc_security_group_ingress_rule" "allow_tls_ipv4" {
  security_group_id = aws_security_group.web-server-sg.id
  cidr_ipv4         = aws_vpc.main.ipv4_cidr_block
  from_port         = 22
  ip_protocol       = "tcp"
  to_port           = 22
}

resource "aws_vpc_security_group_egress_rule" "allow_all_traffic_ipv4" {
  security_group_id = aws_security_group.allow_tls.id
  cidr_ipv4         = "0.0.0.0/0"
  ip_protocol       = "-1" # semantically equivalent to all ports
}


```













