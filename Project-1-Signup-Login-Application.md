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
`ssh2`
`ssh`
`Kuberenetes deploy-custom` Old version should be used (1.0)

Setup the connection for Kubernetes Master



Create Freestyle project 

Git = https://github.com/Raam043/Sign_Up_-_Sign_In.git

Docker Image build and push to registry

![image](https://user-images.githubusercontent.com/111989928/200188455-df406846-e795-489f-90d9-542f803e3b55.png)

Stopping existing resources 

![image](https://user-images.githubusercontent.com/111989928/200188477-51c59996-d68b-43f3-9435-e1cd213295b5.png)

Create Pod @ kubernetes Master

![image](https://user-images.githubusercontent.com/111989928/200188526-38fc4b91-f72d-4ab4-8df4-653c17952c99.png)

Deploy / Service start for the built docker image

![image](https://user-images.githubusercontent.com/111989928/200188558-df71b32c-0348-42b6-8e2c-0b94a0acf11f.png)


Kubenetes Master screen before deployment
![image](https://user-images.githubusercontent.com/111989928/200178676-c992b34b-cad8-49fd-bfe6-1a40b1f8e901.png)

After deployment 

![image](https://user-images.githubusercontent.com/111989928/200187426-e23b577f-5568-479c-9f91-7c863adbfa75.png)



`Output`


![image](https://user-images.githubusercontent.com/111989928/200188753-d92aad4c-f660-47a3-89a9-7864702598ac.png)


![image](https://user-images.githubusercontent.com/111989928/200188737-177e2ba2-bd7e-40d4-b754-6b0c446a1e98.png)

![image](https://user-images.githubusercontent.com/111989928/200188772-487730b2-5961-47db-806a-fe8b4366f11e.png)






