# Project :-  Installing Tomcat Web application on Docker container using Docker + Jenkins

Release ec2 server 
make sure below are open on the server 
`8080`, `8090`, `80` & `22`

## Install Docker server
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

## Install Jenkins
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
