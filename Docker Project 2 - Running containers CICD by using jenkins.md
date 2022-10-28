# Project Map
![Project Map](https://user-images.githubusercontent.com/111989928/198692998-c40d170d-8d66-4e12-9454-08f01bb592a8.png)






Deploying application on direct container

# Install Docker
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
yum install git -y
```

Run Jenkins application on :8080 port and setup the profile

Create project repo on GitHub with `index.html`

Create pipeline project with Poll SCM trigger

Script for 1st run
```sh
docker pull raam043/tomcat
docker run --name tomcat -d -p 8090:8080 raam043/tomcat
docker cp index.html tomcat:/usr/local/tomcat/webapps/RRR

docker pull nginx
docker run --name nginx -d -p 80:80 nginx
docker cp index.html nginx:/usr/share/nginx/html/

docker pull httpd
docker run --name httpd -d -p 443:80 httpd
docker cp index.html httpd:/usr/local/apache2/htdocs
```

Script for 2nd run + Continuous 
```sh
docker pull raam043/tomcat
docker stop tomcat
docker rm -f tomcat
docker run --name tomcat -d -p 8090:8080 raam043/tomcat
docker cp index.html tomcat:/usr/local/tomcat/webapps/RRR

docker pull nginx
docker stop nginx
docker rm -f nginx
docker run --name nginx -d -p 80:80 nginx
docker cp index.html nginx:/usr/share/nginx/html/

docker pull httpd
docker stop httpd
docker rm -f httpd
docker run --name httpd -d -p 443:80 httpd
docker cp index.html httpd:/usr/local/apache2/htdocs
```

Successfully Build your project👍😍
