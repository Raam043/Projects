## Terraform to AWS Pipeline process

Prerequisition:
1. Jenkin server
2. Git repo with Terraform configuration files ( main.tf / test.tfvars / variables.tf)

Refer the repo **[Terraform automation](https://github.com/Raam043/Git-jenkins-terraform-aws_infra)** for reference 

Install the terraform application on terraform server

Download the zip file for installa teraform on server

https://www.terraform.io/downloads


Run below commands on jenkins / terraform server

```sh
wget https://releases.hashicorp.com/terraform/1.3.2/terraform_1.3.2_linux_amd64.zip


unzip terraform_1.3.2_linux_amd64.zip


mv terraform /usr/bin/


terraform --version
```

Run Jenkins and download the "Terraform" plugin on jenkins


Set the Global tool configuration setting for Terraform > Terraform > name = terraform-11 path = /usr/bin/



# Create new Pipeline project and add below script for run the terraform.

```sh
pipeline {
    agent any
     tools {
  terraform 'terraform-11'
}
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Raam043/Git-jenkins-terraform-aws_infra.git'
            }
        }
        stage('Terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform plan') {
            steps {
                sh 'terraform plan -var-file=test.tfvars'
            }
        }
        stage('Terraform apply') {
            steps {
                sh 'terraform apply -var-file=test.tfvars -auto-approve'
            }
        }
    }
}

```

Save and build project 

Check the Aws VPC console for confirmation 

Add `ec2.tf`, `s3.tf`, `sns.tf` & etc .... for further services execution

To delete vpc add 'pipeline script as shown below'
```sh
        stage('Terraform apply') {
            steps {
                sh 'terraform apply -destory -var-file=test.tfvars -auto-approve'
            }
        }
```
