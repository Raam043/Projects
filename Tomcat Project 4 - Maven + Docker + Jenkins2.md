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
cd
ssh-keygen
```
![image](https://user-images.githubusercontent.com/111989928/199684141-57303825-762a-4bfc-b9b8-b3556fbe7754.png)


```sh
cat /root/.ssh/id_rsa.pub>>/root/.ssh/authorized_keys
chmod 755 /root/.ssh/authorized_keys
cat /root/.ssh/id_rsa.pub>>/home/ec2-user/.ssh/authorized_keys
chmod 755 /home/ec2-user/.ssh/authorized_keys
```
and try ssh localhost and exit 
```sh
ssh localhost
```
![image](https://user-images.githubusercontent.com/111989928/199684319-1299b13f-89a6-4ae5-bbfc-e0663d22c5f6.png)
