# Project :- Deploying Webapp on tomcat docker container using Maven + Jenkins - CICD - Part 2

Release 1 linux server for Ansible master 

Install Ansible + Docker 
```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
yum install git -y
amazon-linux-extras install ansible2 -y
```

Generate SSH password to set the passwordless connection
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

Now Create the `AMI` for master to release nodes
![image](https://user-images.githubusercontent.com/111989928/199684790-6867d599-85b3-48c8-b7e2-03359b514a86.png)

Release 2+more nodes using Ansible master AMI 

![image](https://user-images.githubusercontent.com/111989928/199685788-fe141f37-41a0-47ae-8c86-b56d1083e262.png)

Connect with nodes from Master using ssh + nodes public ip
![image](https://user-images.githubusercontent.com/111989928/199686227-5a499e76-5d58-4d0b-8ffb-fe0ba319db2e.png)

Connect with nodes from Master using ansible playbook - nodes private ip
![image](https://user-images.githubusercontent.com/111989928/199686769-2fb3569a-0cfc-4a5c-875d-0f61d5454b38.png)


Create `Pipeline Project` and add below script 

```sh
pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Raam043/CICD-Git-jenkins-maven-tomcat.git'
            }
        }
        stage('Image Pushing to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'DP', variable: 'DP')]) {
                    sh 'docker tag test raam043/tomcat-project'
                    sh 'docker login -u raam043 -p ${DP}'
                    sh 'docker push raam043/tomcat-project'
            }
        }
        }
        stage('Ansible Playbook for webapp deploy') {
            steps {
                ansiblePlaybook credentialsId: 'Ansible-ssh', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'nodes.inv', playbook: 'tomcat-project.yml'
            }
        }
    }
}
```

![image](https://user-images.githubusercontent.com/111989928/199778371-4b9f44f0-4f28-4f0f-a8f2-14fd3fa00c8b.png)


