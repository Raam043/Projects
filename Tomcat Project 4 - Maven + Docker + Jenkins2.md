# Project :- Deploying Webapp on tomcat docker container using Maven + Jenkins - CICD - Part 2

Release 1 linux server for Ansible master 

Install Docker and make ssh setting
```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
yum install git -y
```

Generate SSH password 
```sh
ssh-keygen
```
```sh
cat /root/.ssh/id_rsa.pub>>/root/.ssh/authorized_keys
chmod 755 /root/.ssh/authorized_keys
cat /root/.ssh/id_rsa.pub>>/home/ec2-user/.ssh/authorized_keys
chmod 755 /home/ec2-user/.ssh/authorized_keys
```
