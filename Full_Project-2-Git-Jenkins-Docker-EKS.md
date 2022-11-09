# Git - Jenkins - Docker - EKS - Kubernetes nodes




## 1. Set up the EKS on AWS - Please refer the repository for setup procedures 👉 **[EKS Setup](https://github.com/Raam043/Projects/blob/39acd2b88e39e168fc73c13a6adb9a2947152a6b/Kubernetes_Project-2-EKSCTL.md)**

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


## 3. Set up the Jenkins profile / application and download the Kubernetes plugin by Advanced setting

https://github.com/Raam043/Sign_Up_-_Sign_In/raw/main/kubernetes-cd.hpi

Deploy plugin at advance setting

![image](https://user-images.githubusercontent.com/111989928/200826362-d76bf863-1837-4a67-baa4-6061dfc11bb8.png)




## 4. Add the Credentials of EKS Master


Run the command on `EKS Master` to get config details 
```sh
cat /root/.kube/config
```

Output 

![image](https://user-images.githubusercontent.com/111989928/200827198-b37bfc65-2327-49f1-98c0-f525bf341298.png)

Open Jenkins ➡ Manage Credentials ➡ System ➡ Global Credentials and add EKS Master config details


![image](https://user-images.githubusercontent.com/111989928/200828155-e65d50c9-76ca-4b6e-8c09-0067dded7c39.png)


















