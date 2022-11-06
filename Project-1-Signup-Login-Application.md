# Signup-Login Application CICD

### Requirements to complete CICD:- 

AWS_3_Serrvers : 1 for Jenkins + Docker | 1 for Kubernetes Master | 1 for Kubernetes worker/node

Git_Repo : Application files + Dockerfile

Jenkins : To create Pipeline

Docker : To build image and push image to DockerHub

Kubernetes : Running Docker containers

1. Release one server to install Jenkins + Docker

```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
yum install git -y
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum upgrade
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
```
