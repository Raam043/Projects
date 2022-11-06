# Signup-Login Application CICD (HTML+CSS Project)

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
2. Release 2 server to make Kubernetes cluster set up

For Setup the Kubernetes Cluster please follow instructions from this Document => **[Kubernetes Cluster Setup](https://github.com/Raam043/Projects/blob/main/Kubernetes_Project-1-Kubeadm.md)**

3. Now Setup the Jenkins and install below Plugins







Kubenetes Master screen before deployment
![image](https://user-images.githubusercontent.com/111989928/200178676-c992b34b-cad8-49fd-bfe6-1a40b1f8e901.png)

