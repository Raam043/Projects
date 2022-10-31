## Project :- CICD for Running customized image containers on Ansible nodes


# Project Map
![Project Map](https://user-images.githubusercontent.com/111989928/198696150-0ec910dc-096f-402e-8917-2c719133825e.png)




Requirements:
1. Master EC2 instance with Docker,Jenkins and Ansible installed (SSH key configuration)
2. Nodes with docker+pip installed (copy of master ssh id.rsa.pub on all nodes)
3. Git Repo with Dockerfile, Application file, Anisble yaml amd nodes inverntory files

`STEP - 1`
# Install Docker
```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
```
Generate key for connecting all nodes along with docker
```sh
cd
ssh-keygen
```
```sh
cat /root/.ssh/id_rsa.pub>>/root/.ssh/authorized_keys
chmod 755 /root/.ssh/authorized_keys
cat /root/.ssh/id_rsa.pub>>/home/ec2-user/.ssh/authorized_keys
chmod 755 /home/ec2-user/.ssh/authorized_keys
ssh localhost
```
`STEP - 2`
Now create the AMI ( image for coping the nodes)
With AMI create 2 + more nodes
![image](https://user-images.githubusercontent.com/111989928/199017364-7fe7bd3d-f624-4a1f-b754-d01c52a9e797.png)

`STEP - 3`
# Install Jenkins
```sh
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum upgrade
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
```

# Install Ansible 
```sh
amazon-linux-extras install ansible2 -y
```

# Install Git
```sh
yum install git -y
```

`STEP - 4`
Create Git Repo with `Dockerfile`, `Nodes.inv`, `httpd_container.yml` & your own`index.html`

**[Dockerfile](https://github.com/Raam043/Jenkins-project-test/blob/main/Dockerfile)**
```sh
FROM httpd
COPY index.html /usr/local/apache2/htdocs
EXPOSE 80
```

**[nodes.inv](https://github.com/Raam043/CICD_Project-Deploy_Webapp_to_docker_containers_using_Ansible-LINUX/blob/2360cc582c9fe661424b4dde6e6ffb77d44c3547/nodes.inv)** Copy all nodes private ip address and paste on below nodes.inv file along with "ansible_user=ec2-user"
```sh
[rnb]
172.31.35.232 ansible_user=ec2-user
172.31.39.215 ansible_user=ec2-user
```

**[Container.yml](https://github.com/Raam043/Jenkins-project-test/blob/main/httpd_container.yml)** Ansible playbook file for deploy application on all nodes
```sh
---
- hosts: rnb
  become: True
  tasks:
        
    - name: Run docker container
      command: "docker stop raam043/test_project:latest"
      command: "docker rm -f raam043/test_project:latest"
      command: "docker image rm -f raam043/test_project:latest"
      
    - name: Run docker container
      docker_container:
        name: WebServer
        image: raam043/test_project:latest
        state: started
        exposed_ports:
        - "80"
        ports:
        - "80:80"
 ```
 

Now open jenkins add plugins `Ansible` set the path as "/usr/bin" at Global tools configuration setting


Create pipeline project with Build Triggers as Poll SCM "* * * * *"

Please refer the below script 
```sh

pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Raam043/Jenkins-project-test.git'
            }
        }
        stage('Docker image Build') {
            steps {
                sh 'docker stop docker_ramesh'
                sh 'docker rm -f docker_ramesh'
                sh 'docker image rm -f docker_ramesh'
                sh 'docker build -t docker_ramesh .'
            }
        }
        stage('Docker Conatiner run') {
            steps {
                sh 'docker run -d --name docker_ramesh -p 80:80 docker_ramesh'
                sh 'docker tag docker_ramesh raam043/test_project:latest'
            }
        }
        stage('Image push to DockerHub & Deploying on Ansible nodes') {
            steps {
                withCredentials([string(credentialsId: 'DP', variable: 'DP')]) {
                    sh 'docker login -u raam043 -p ${DP}'
                    sh 'docker push raam043/test_project:latest'
                    ansiblePlaybook credentialsId: 'Ansible-private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'nodes.inv', playbook: 'httpd_container.yml'
            }
        }
        }
    }
}


```

`If Pipeline failed`
run te commands only for jenkins to grant docker access for ec2-user
```sh
sudo chmod 666 /var/run/docker.sock
usermod -aG docker ec2-user
chown -R ec2-user:ec2-user /root/
chown -R ec2-user:ec2-user /opt/
```

For adding DockerHub credentials please follow below steps
Click on `Pipeline Syntax` > select `withCredentials: Bind credentials to variables` > Variable = "Docker-pp" > add Credentials = using jenkins Credentials 
select `Secrete text` add Password on text field save and generate script

ouput
withCredentials([string(credentialsId: 'Docker-pp', variable: 'Docker-pp')]) {
    // some block
}

remove "some block line item and paste on pipeline script

Save and run the job.







 
 
 Project success Enjoy 👍😊
