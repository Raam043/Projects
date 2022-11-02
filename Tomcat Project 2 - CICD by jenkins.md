# Application deploying on Tomcat using Tomcat manager role and user by Jenkins & Maven

## 1. Release 2 Linux servers
1 for Jenkins + Maven

1 for Tomcat

## 2. `On Server 1` Install jenkins + Maven
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
cd /opt
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

Setup the tools @ manage jenkins > Global tool configuration

For `Java`

![image](https://user-images.githubusercontent.com/111989928/199538575-3656cd22-a254-477b-b49f-8beec3870d8b.png)

For `Maven`

![image](https://user-images.githubusercontent.com/111989928/199538740-89981c5f-c5f4-42d0-a0d5-66bfa2e685e3.png)



## 3. `On Server 2 - Tomcat` Install Tomcat + create work directory on tomcat/webapps
```sh
yum update -y
cd /opt
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.68/bin/apache-tomcat-9.0.68.tar.gz
tar -xzvf apache-tomcat-9.0.68.tar.gz
mkdir /opt/apache-tomcat-9.0.68/webapps/RRR
```

Edit the `Context.xml` file to enable public login for Host manager

```sh
vi /opt/apache-tomcat-9.0.68/webapps/host-manager/META-INF/context.xml
```
```sh
vi 
```
```diff
- Before
```
![image](https://user-images.githubusercontent.com/111989928/199543146-bd741583-aa7d-46c3-82b6-ce81f3eac789.png)


```diff
+ After
```
![image](https://user-images.githubusercontent.com/111989928/199544137-4797e01d-2150-4e83-af59-d190ef6dab53.png)

Add `<!--` before "<Valve ClassNam and 
                          0:0:1" (2nd line end) `-->`

