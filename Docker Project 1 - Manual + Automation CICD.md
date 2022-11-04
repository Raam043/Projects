## Project :- Installing Docker, Running Images and Containers ( Manual + Automation CICD pipeline)
![image](https://user-images.githubusercontent.com/111989928/199915509-0d3f5c0f-3d26-417d-bee4-f73f5b6e63cb.png)



# 1 Docker Installation - Pull Images and Run Containers

Release Linux server and install docker 

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
```

Confirm the installation
```sh
docker --version
```

Get list of Docker images
```sh
docker images
```

Get list of Docker containers
```sh
docker ps -a
```

Pull the image from DockerHub
```sh
docker pull httpd
```

Run images list and you can able see recent downloaded httpd image
```sh
docker images
```

Run container for httpd
```sh
docker run --name httpd-c -d -p 80:80 httpd
```

For confirmation > Open new tab and paste server_ip 

Deploy application on conatainer 
```sh
docker exec -it <container_id> bash
docker cp index.html <container_id>:/usr/local/apache2/htdocs
docker cp index.html <container_id>:/usr/share/nginx/html/
docker cp index.html <container_id>:/usr/local/tomcat/webapps/TEST
```

To stop  conatiner 
```sh
docker stop <container_id>
```

To start conatiner
```sh
docker start <container_id>
```
To delete conatiner
```sh
docker rm <container_id>
```

To delete images
```sh
docker rmi <image_name> OR <image_id>
```


# 2 Docker CICD - using Jenkins Application

Prerequisition:
1. Docker installed server

Install jenkins
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

Run Jenkins application on :8080 port and setup the profile

Create project repo on GitHub
In my case i have used repo https://github.com/Raam043/Docker-CICD.git

Add new file = `Dockerfile`
```sh
FROM nginx
COPY index.html /usr/share/nginx/html/
EXPOSE 80
```
add `index.html` to repo


Create pipeline project with Poll SCM trigger

`Pipeline Script 1st time run`
```sh
pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Raam043/Test-project.git'
            }
        }
        stage('Docker Image Build') {
            steps {
                sh 'docker build -t myapp .'
            }
        }
        stage('Docker Run Container') {
            steps {
                sh 'docker run --name myapp -d -p 80:80 myapp'
            }
        }
        
    }
}
```


`Pipeline Script 2nd and continue to build jobs`

```sh
pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Raam043/Test-project.git'
            }
        }
        stage('Docker Image Build') {
            steps {
                sh 'docker stop myapp'
                sh 'docker rm -f myapp'
                sh 'docker image rm -f myapp'
                sh 'docker build -t myapp .'
            }
        }
        stage('Docker Run Container') {
            steps {
                sh 'docker run --name myapp -d -p 80:80 myapp'
            }
        }
        
    }
}
```
