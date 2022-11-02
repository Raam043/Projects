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
* Deploy to container

Setup the tools @ manage jenkins > Global tool configuration

For `Java`

![image](https://user-images.githubusercontent.com/111989928/199538575-3656cd22-a254-477b-b49f-8beec3870d8b.png)

For `Maven`

![image](https://user-images.githubusercontent.com/111989928/199538740-89981c5f-c5f4-42d0-a0d5-66bfa2e685e3.png)



## 3. `On Server 2 - Tomcat` Install Tomcat + create work directory on tomcat/webapps
```sh
yum update -y
cd /opt
yum install java -y
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.68/bin/apache-tomcat-9.0.68.tar.gz
tar -xzvf apache-tomcat-9.0.68.tar.gz
mkdir /opt/apache-tomcat-9.0.68/webapps/RRR
cd /opt/apache-tomcat-9.0.68/bin
./startup.sh
```
Open Server IP : 8080 on new tab
![image](https://user-images.githubusercontent.com/111989928/199546600-db8acd8b-c491-4063-8ecf-6f47cca27180.png)

Tomcat application is runng and open "Manager App" it will denied to open 

![image](https://user-images.githubusercontent.com/111989928/199546993-96aa595b-12a5-4810-b928-9a2952b0129d.png)


Lets enable the Manager login with editing the `Context.xml` file to enable public login for Host manager (Edit the both files same as shown below)

```sh
vi /opt/apache-tomcat-9.0.68/webapps/host-manager/META-INF/context.xml
```
```sh
vi /opt/apache-tomcat-9.0.68/webapps/manager/META-INF/context.xml
```
OR you can serach mannualy run command "find / -name context.xml"
```diff
- Before
```
![image](https://user-images.githubusercontent.com/111989928/199543146-bd741583-aa7d-46c3-82b6-ce81f3eac789.png)
```diff
+ After
```
Add ` <!-- ` before <Valve ClassNam and 
                          0:0:1> (2nd line end) ` --> `
![image](https://user-images.githubusercontent.com/111989928/199544536-01499aef-84c4-496c-9949-525466342aef.png)
    
<
Restart tomcat
```sh
cd /opt/apache-tomcat-9.0.68/bin
./shutdown.sh
./startup.sh
```
After editing both context.xml files now open the manager app it will open
 
![image](https://user-images.githubusercontent.com/111989928/199548711-21756b74-44d2-4873-afe0-0b1b6ab5ca82.png)

 Now create Users with Roles
 ```sh
vi /opt/apache-tomcat-9.0.68/conf/tomcat-users.xml
```
Add Below roles 
```sh
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="ramesh" password="ramesh123" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
```
```diff
- Before
```
![image](https://user-images.githubusercontent.com/111989928/199549788-13e33905-5e20-40ef-af59-5bed6e54ca50.png)

```diff
+ After
```
![image](https://user-images.githubusercontent.com/111989928/199550166-9e6822b6-71e8-4eb6-99fd-aac7ca39e47c.png)

    
Restart tomcat
```sh
cd /opt/apache-tomcat-9.0.68/bin
./shutdown.sh
./startup.sh
```
Now you can login into Manager App with Credentials ( please remember credentials - same can be used on jenkins pipeline)

![image](https://user-images.githubusercontent.com/111989928/199550677-aa028f58-9827-40e2-a9bc-4e6da8310507.png)

 ![image](https://user-images.githubusercontent.com/111989928/199550879-428a3c33-5d1f-42ef-9af5-ded547fae0a0.png)
    
## 4. Now lets build Job with creating Git Repo + Jenkins Project Pipeline

Inn my case i have created repo with Maven `pom.xml` with dependency 
    
![image](https://user-images.githubusercontent.com/111989928/199551830-ca01ecb5-e7ea-402e-b045-f78841b0c266.png)


Create "Maven Project"  on jenkins
![image](https://user-images.githubusercontent.com/111989928/199551241-682cd195-09a9-4727-b573-129a37f21c29.png)

`Source Code Management` = Git with main Branch
![image](https://user-images.githubusercontent.com/111989928/199552014-0318097f-842b-43f8-8f98-0f0de28de376.png)

    
`BUILD` =  pom.xml and goal will be blank
![image](https://user-images.githubusercontent.com/111989928/199552287-ec66d95b-efab-45b1-95c4-413cb2db36ed.png)

 
`Post-build actions` = Deploy war/ear to a container
    
![image](https://user-images.githubusercontent.com/111989928/199553064-192267cd-3454-4b52-b896-fe5e7d7a5b7f.png)

    
WAR/EAR files = **/*.war
Context path = ""
Containers = Browse - Tomcat 9.x Remote
![image](https://user-images.githubusercontent.com/111989928/199553431-2b3d3254-dc69-400d-9939-27eb1f694398.png)

Add Credentials using jenkins symbol
![image](https://user-images.githubusercontent.com/111989928/199553822-e454b65a-c064-4207-a874-47aeb03a5447.png)
![image](https://user-images.githubusercontent.com/111989928/199553951-1f82967a-db6b-4dc0-8ff9-cf77c2d18a00.png)

    
Add URL of Tomcat Server with Port

![image](https://user-images.githubusercontent.com/111989928/199554059-4b5fd610-71f6-4b3d-a7d9-b1073d7bf6ef.png)
    
 
    
