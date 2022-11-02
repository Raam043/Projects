# Application deploying on Tomcat using Tomcat manager role and user by Jenkins & Maven

Release 2 server
1 for Jenkins + Maven
1 for Tomcat

## 1. `On Server 1` Install jenkins + Maven
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
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz
tar -xzvf apache-maven-3.8.6-bin.tar.gz
```

Setup the environment setting for Java and Maven
