# Project :- DNS Automation by Terraform - EC2 to DNS



### Create Folder on local Desktop or Virtual machine. Install `Terraform`  👉🏻 **[Installation steps](https://github.com/Raam043/Applications-Installation/blob/cd2f64000965f8254870a7263dba5ccbcddfc407/Terraform/Terraform%20installation%20%20on%20local%20system.MD)**

Setup terraform configuration files on folder

## 1 - `terraform.tfvars` = IAM "Access_Key" & "Secret_Key" 

```sh
access_key = "abc"
secret_key = "xyz"
```

## 2 - `variable.tf` = Variables

```sh

variable "access_key" {}
variable "secret_key" {}
variable "region" {
    default = "us-east-2"
}
variable "ami_id" {
    default = {
        us-east-2 = "ami-0beaa649c482330f7"
        us-east-1 = "ami-097a2df4ac947655f"
    }
  
}

```

## 3 - `provider.tf` = Cloud provider details with above Access & Secret keys

```sh

provider "aws" {
    region = "${var.region}"
    access_key = "${var.access_key}"
    secret_key = "${var.secret_key}"
  
}
```


## 4 - `main.tf` = To create EC2 instances + route 53 for DNS

```sh

# Creating Linux ec2 server ----------------------------------------------------------------------------
resource "aws_instance" "web" {
    count = 1
    ami = "ami-0beaa649c482330f7"
    instance_type = "t2.micro"
    key_name = "USIS"
    security_groups    = ["All Traffic","default"]
    user_data = <<-EOF
#! /bin/bash
yum update -y
amazon-linux-extras install nginx1.12 -y
service nginx start
wget https://github.com/Raam043/Pipeline-HTML/archive/refs/heads/master.zip
sudo unzip master.zip
sudo mv Pipeline-HTML-master/index.html /usr/share/nginx/html/
    EOF
    tags = {
    Name = "Ramesh-Linux"

  }
}

# Output for Servers Public Ip addresses
output "servers_publc_ip" {
  value = "${aws_instance.web.*.public_ip}"
}

# Route53 Creation---------------------------------------------------------------------------------------
#Creating Hosted Zone
resource "aws_route53_zone" "saikrishna" {
    name = "saikrishna.ga"

    tags = {
      Environment = "dev"
    }
  
}

# Generate Name servers (4 servers) ---------------------------------------------------------------------
output "name_server" {
    value = aws_route53_zone.saikrishna.name_servers
}

# Creating "A" record (final) ---------------------------------------------------------------------------
resource "aws_route53_record" "www" {
    zone_id = aws_route53_zone.saikrishna.zone_id
    name = "www.saikrishna.ga"
    type = "A"
    ttl = "60"
    records = "${aws_instance.web.*.public_ip}"
  
}

```


<img width="683" alt="Screenshot 2022-11-18 at 9 42 35 PM" src="https://user-images.githubusercontent.com/111989928/202750471-828360da-6bc1-4eee-8233-f503f1199b05.png">

## Terraform plan and Apply

```sh 
terraform init
terraform plan
```

If all are set go ahead for apply

```sh
terraform apply -auto-approve
```

<img width="911" alt="Screenshot 2022-11-18 at 9 56 11 PM" src="https://user-images.githubusercontent.com/111989928/202753290-792573e6-2153-49d5-b8cf-f93b0309aea2.png">







## `Instance & Route53 crated and generated name 4 servers`

<img width="902" alt="Screenshot 2022-11-18 at 9 57 09 PM" src="https://user-images.githubusercontent.com/111989928/202753502-5e3d48ae-e74d-4c16-a11e-5908fe3030a2.png">


<img width="1272" alt="Screenshot 2022-11-18 at 10 09 11 PM" src="https://user-images.githubusercontent.com/111989928/202756408-8842f85e-c7d1-4c60-ba29-965b24e2f04d.png">


<img width="985" alt="Screenshot 2022-11-18 at 10 12 03 PM" src="https://user-images.githubusercontent.com/111989928/202756867-826784d3-721e-40ed-a975-ae0ecbc1846a.png">



## Add name servers on `Domain name servers setting`

<img width="1020" alt="Screenshot 2022-11-18 at 10 06 01 PM" src="https://user-images.githubusercontent.com/111989928/202755801-4f409155-353d-45de-b858-ac7cc6f638a9.png">


# Output of Project ( DNS activated and listening sucessfully) 

<img width="1280" alt="Screenshot 2022-11-18 at 10 14 30 PM" src="https://user-images.githubusercontent.com/111989928/202757504-ccc84169-6ed8-4b14-863c-1c0ceaa518bd.png">


