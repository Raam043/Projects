# Project :-  Installing Tomcat Web application on Docker container using Docker + Jenkins

Release 2 ec2 servers 

Server 1 for Docker + Tomcat
Server 2 for jenkins

make sure below ports are open on the both servers
`8080`, `8090`, `80` & `22`

## Install Docker on `Server 1`
```sh
yum udpate -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
sudo chmod 666 /var/run/docker.sock
usermod -aG docker ec2-user
chown -R ec2-user:ec2-user /root/
chown -R ec2-user:ec2-user /opt/
```

Create Docker User and password ( it will be used on jenkins ssh connection)
```sh
useradd ramesh
passwd ramesh
usermod -aG docker ramesh
```
Set ssh setting to enable passwordless connection from jenkins
```sh
vi /etc/ssh/sshd_config
```
Change the setting as per below

`Before`

![image](https://user-images.githubusercontent.com/111989928/198711105-0672a166-bf18-4922-a0dd-074bf7f75f8d.png)

`After`

![image](https://user-images.githubusercontent.com/111989928/198711366-3b7384d3-e18c-42c4-b966-149aeaefbcd6.png)

Edit and Save 

```sh
service sshd restart
```


## Install Jenkins on `Server 2`
```sh
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum upgrade
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
yum install git -y
```
Open jenkins Application (Server 2 _ ip:8080)

Download the `Deploy to container` & `Publish over ssh` Plug-in (Manage Jenkins > Plugin Manager > search the plugins and install it without restart)

Go to `Manage Jenkins` > `Configure System` > `SSH Server` 

Name = docker
Hostname = Docker server "Private IP" address ( Server 1)
User Name = ramesh    (docker user id and password which created on docker server 1)
Passphrase / Password = "password"

Click on Test connection





Now Create Freestyle Project

`Source Code Management`= Git

Repo = https://github.com/Raam043/CICD-GiT-Jenkins-Maven-Docker-Tomcat.git 

Branch = main


`Build Triggers`= Poll SCM 

Schedule = * * * * *


`Post-build Actions`= Build Steps














