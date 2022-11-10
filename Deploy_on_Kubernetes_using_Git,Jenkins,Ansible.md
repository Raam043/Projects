# Project :- Deploy on Kubernetes using Git, Jenkins, Ansible


Prerequisitions :- 

1. Kubenetes Cluster Setup 👉**[Setup Demo](https://github.com/Raam043/Projects/blob/a303b301e5055394fa2d4960b715806f290b613c/Kubernetes--KUBEADM....md)**

2. Jenkins + Maven Installed Server

3. Ansible Installed Server

4. Git installation on all the servers.




## 1 - Create `Maven Project` for `CI`

![image](https://user-images.githubusercontent.com/111989928/201087075-88d5d63c-fbcb-4b6b-8295-1d2eb83bb3d5.png)


1. Git Repository used 👉 https://github.com/Raam043/CICD-Git-jenkins-maven-tomcat.git

`Please refer the Repo for deployment files`

![image](https://user-images.githubusercontent.com/111989928/201087360-d0b575e1-2696-45d7-8875-0366807711d4.png)




2. Poll SCM for Immediate Build Triggers from GitHub

![image](https://user-images.githubusercontent.com/111989928/201087496-11a6402d-2023-41b7-a3c2-3dcd0da1e38e.png)



3. POM file for Maven code Build 

![image](https://user-images.githubusercontent.com/111989928/201087570-30b355d4-6b35-4978-a639-454fd6dd2e40.png)



4. Sheel Execute for Coping the webapp.war file and removing the old docker images

```sh
cp /var/lib/jenkins/workspace/Git-Jenkins-Maven-Docker-Ansible-Kubernetes-CI/webapp/target/webapp.war //var/lib/jenkins/workspace/Git-Jenkins-Maven-Docker-Ansible-Kubernetes-CI

docker image rm -f raam043/tomcat-project & ls
```

![image](https://user-images.githubusercontent.com/111989928/201087927-905acb50-06ab-4d6e-aa70-491a8355e728.png)




5. Docker Build and Publish with DockerHub Credentials 

![image](https://user-images.githubusercontent.com/111989928/201088122-f0dfe767-3195-4b3c-9ad2-227fd0a2267d.png)

![image](https://user-images.githubusercontent.com/111989928/201088185-97e92f8a-1d77-4206-ab5c-6ca78c43857d.png)





6. Build other project for CD

![image](https://user-images.githubusercontent.com/111989928/201088339-9aaeb69c-150f-430b-843c-a983fa84d7b5.png)




## 2 - Create `Freestyle Project` for `CD`


1. Build Steps = Send files or execute commands over SSH
Connect with Ansible using (user = root  passwd = ramesh123 > ssh connection )

![image](https://user-images.githubusercontent.com/111989928/201089027-012dcf95-313d-49e0-b1c1-d8c445fe5d10.png)


![image](https://user-images.githubusercontent.com/111989928/201089090-a764d95e-5e71-462f-a626-ccabd424fb00.png)

```sh
rm -rf /opt/kube-test
mkdir /opt/kube-test
cd /opt
rm -rf CICD-Git-jenkins-maven-tomcat
git clone https://github.com/Raam043/CICD-Git-jenkins-maven-tomcat.git
cp CICD-Git-jenkins-maven-tomcat/* /opt/kube-test
cd /opt/kube-test
ansible-playbook -i hosts kube-deploy.yml
```



Save and Run the Job



`OUTPUT`

![image](https://user-images.githubusercontent.com/111989928/201089331-e68d2c16-e01d-4ea1-829a-2469ed54b3e9.png)

![image](https://user-images.githubusercontent.com/111989928/201089596-990d3c99-9b05-4c93-ad3a-24ef551ec238.png)




