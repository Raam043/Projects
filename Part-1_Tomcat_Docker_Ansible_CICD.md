# Project :- Deploying Webapp on tomcat docker container using Maven + Jenkins - CICD - Part 1

Install Jenkins + docker + Maven + Ansible + Git
```sh
yum update -y
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum upgrade
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
yum install git -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
sudo chmod 666 /var/run/docker.sock
amazon-linux-extras install ansible2 -y
mkdir /opt/maven
cd /opt/maven
wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz
tar -xzvf apache-maven-3.8.6-bin.tar.gz
```

Setup the environment setting for Java and Maven
```sh
cd
vi .bash_profile
```

Add the path as shown below based on version of Java + Maven
```diff
- Before
```

![image](https://user-images.githubusercontent.com/111989928/199534365-046e6aa0-b0b7-419b-993b-aa106527ef3b.png)

```diff
+ After
```

![image](https://user-images.githubusercontent.com/111989928/199535469-b1bf3a7c-3cb4-47b4-97a4-9c7b40c5a4fb.png)
```sh
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.16.0.8-1.amzn2.0.1.x86_64
M2_HOME=/opt/maven/apache-maven-3.8.6
M2=$M2_HOME/bin

PATH=$PATH:$JAVA_HOME:$M2_HOME:$M2:$HOME/bin
```

Now Setup the jenkins app
![image](https://user-images.githubusercontent.com/111989928/199536974-545a8c9a-e286-4591-afd4-391b9afbcb66.png)

Once opened the jenkins download the plugins for maven project @ Manage jenkins > Manage plugins

* Maven Integration
* Maven Invoker
* ssh
* Publish over ssh
* Ansible

Setup the tools @ manage jenkins > Global tool configuration

For `Java`

![image](https://user-images.githubusercontent.com/111989928/199538575-3656cd22-a254-477b-b49f-8beec3870d8b.png)

For `Maven`

![image](https://user-images.githubusercontent.com/111989928/199538740-89981c5f-c5f4-42d0-a0d5-66bfa2e685e3.png)

For `Ansible`

![image](https://user-images.githubusercontent.com/111989928/199682554-37bef8fe-a0be-4f3a-8e68-bbfa70158549.png)



Create "Maven Project"  on jenkins
![image](https://user-images.githubusercontent.com/111989928/199661512-9fdd1f33-3e85-466a-a9db-b470c421f067.png)


`Source Code Management` = Git with main Branch
![image](https://user-images.githubusercontent.com/111989928/199552014-0318097f-842b-43f8-8f98-0f0de28de376.png)

    
`BUILD` =  pom.xml and goal will be blank
![image](https://user-images.githubusercontent.com/111989928/199552287-ec66d95b-efab-45b1-95c4-413cb2db36ed.png)

`POST STEPS` = Execute shell
![image](https://user-images.githubusercontent.com/111989928/199665184-718d138d-80ed-4db4-8e22-124faaad3c16.png)
Coping the webapp.war to project folder and building the images with help of tomcat blank + webapp.war
```sh
cp /var/lib/jenkins/workspace/Tomcat-Maven-Docker/webapp/target/webapp.war /var/lib/jenkins/workspace/Tomcat-Maven-Docker/
docker stop test & docker rm -f test & docker image rm -f test & docker build -t test .
docker run --name test -d -p 8090:8080 test
```

Output
![image](https://user-images.githubusercontent.com/111989928/199666073-f40205f3-3b5d-4b4e-a7f5-29c1b60879a7.png)

