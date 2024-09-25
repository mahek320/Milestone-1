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

## Terafform init

terraform init

terraform validate (*The configuration is valid*)

terraform plan

terraform apply 

## Connect the new isntance on a new terminal 

## security group (SSH, All traffic)

yum install httpd -y








