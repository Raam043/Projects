# Git - Jenkins - Docker - EKS - Kubernetes nodes




## 1. Set up the EKS on AWS - Please refer the repository for setup procedures = **[EKS Setup](https://github.com/Raam043/Projects/blob/39acd2b88e39e168fc73c13a6adb9a2947152a6b/Kubernetes_Project-2-EKSCTL.md)**

`On EKS Master`
Connect Maste by ssh and run the below command to grant permission for Jenkins server to deloy application on EKS.

```sh
kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=system:anonymous
```

and make sure to cluster is setup and ready status.



## 2. Release a server for Jenkins to build pipeline to deliver the Application through Kubernetes 

`On Jenkins server`
Connect to ssh -22 and install Docker + Jenkins + Git 
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
sudo chmod 666 /var/run/docker.sock
```


