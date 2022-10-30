# Project :-  Installing Tomcat Web application on Docker container using `SSH CONNECTION`
Tools required : Linux Servers, GitHub, Docker and Jenkins with ssh plugin

Release 2 ec2 servers 

Server 1 for Docker + Tomcat
Server 2 for jenkins

make sure below ports are open on the both servers
`8080`, `8090`, `80` & `22`

## Install Docker on `Server 1`
```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
sudo chmod 666 /var/run/docker.sock
usermod -aG docker ec2-user
chown -R ec2-user:ec2-user /root/
chown -R ec2-user:ec2-user /opt/
mkdir /opt/docker
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

```diff
- Before 
```
![image](https://user-images.githubusercontent.com/111989928/198711105-0672a166-bf18-4922-a0dd-074bf7f75f8d.png)
```diff
+ After
```
![image](https://user-images.githubusercontent.com/111989928/198711366-3b7384d3-e18c-42c4-b966-149aeaefbcd6.png)

Edit and Save 

Restart the ssh service
```sh
service sshd restart
```
Enable folder access to docker user
```sh
chown -R ramesh:ramesh /opt/docker
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

Download the the below plugins (Manage Jenkins > Plugin Manager > search the plugins and install it without restart)

`Publish over ssh`
`ssh agaent`


Go to `Manage Jenkins` > `Configure System` > `SSH Server` 

Name = docker

Hostname = Docker server "Private IP address" ( Server 1)

User Name = ramesh    (docker user id and password which created on docker server 1)

Passphrase / Password = "password"

Click on Test connection ( If failed check sshd config OR set visudo with docker user)

![image](https://user-images.githubusercontent.com/111989928/198826055-b57b0bc0-5e21-4ab5-8b3b-6f0039090831.png)




Create repo on github with `Dockerfile` and `index.html` (Application file)

![image](https://user-images.githubusercontent.com/111989928/198836945-89d7a4b9-f596-4716-a310-d1c2e44c5008.png)

`Dockerfile` 
```sh
# Pull base image 
From tomcat:9-jre9

# Maintainer 
MAINTAINER "Ramesh NB" 
RUN mkdir /usr/local/tomcat/webapps/myapp
COPY ./index.html /usr/local/tomcat/webapps/myapp
```
`index.html` is customized html coding
![image](https://user-images.githubusercontent.com/111989928/198837047-0dcc18c7-dee9-4b87-a5d2-05d9d9fb011f.png)



Now Create Freestyle Project


`Source Code Management`= Git

Repo = https://github.com/Raam043/Tomcat_installing-over-ssh.git

Branch = main

![image](https://user-images.githubusercontent.com/111989928/198836742-b013ad1f-15c8-4d4b-bab4-852ac70ecd76.png)


`Build Triggers`= Poll SCM 

Schedule = * * * * *

![image](https://user-images.githubusercontent.com/111989928/198826209-1dd3f38c-02ae-4e68-954f-0ee271e7c026.png)


`Pre Steps`= Send files or execute commands over SSH

Server name = Docker ( Drop down from jenkins setting)

Exec command = 
```sh
rm -rf Tomcat_installing-over-ssh
git clone https://github.com/Raam043/Tomcat_installing-over-ssh.git
cd Tomcat_installing-over-ssh
docker stop docker_ramesh;
docker rm -f docker_ramesh;
docker image rm -f docker_ramesh;
docker build -t docker_ramesh .
docker run -d --name docker_ramesh -p 8090:8080 docker_ramesh

```
![image](https://user-images.githubusercontent.com/111989928/198836808-b3a9029a-a938-48ac-b8c9-012f56bbe210.png)

Save the job and run it

Results

Tomcat is running at `Server_IP:8090`
![image](https://user-images.githubusercontent.com/111989928/198837101-f67ee652-b94c-4039-a9ca-16cb4a34f4f5.png)



Myapp is running at `Server_IP:8090/myapp`
![image](https://user-images.githubusercontent.com/111989928/198837203-7a3cc60a-8d14-4521-8f48-9c68d56e47fd.png)

```diff
- Ramesh
```
```diff
+ Lokesh
```
```diff
! Yellow
```
```diff
@@ Purple @@
```
