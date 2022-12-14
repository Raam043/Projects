# Signup-Login Application CICD (HTML+CSS Project)


![image](https://user-images.githubusercontent.com/111989928/200379298-29db6f9d-f9e9-43b8-ad99-89796d960f3b.png)




### Requirements to complete CICD:- 

AWS_3_Serrvers : 1 for Jenkins + Docker | 1 for Kubernetes Master | 1 for Kubernetes worker/node

Git_Repo : Application files + Dockerfile

Jenkins : To create Pipeline

Docker : To build image and push image to DockerHub

Kubernetes : Running Docker containers

## 1. Release one server to install Jenkins + Docker

```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
yum install git -y
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum upgrade
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
```
## 2. Release 2 server to make Kubernetes cluster set up

For Setup the Kubernetes Cluster please follow instructions from this Document => **[Kubernetes Cluster Setup](https://github.com/Raam043/Projects/blob/main/Kubernetes_Project-1-Kubeadm.md)**

set `root` password @ Kubernetes Master ( it will use on jenkins ssh connection)
```sh
passwd root
```
Enter password & Set ssh setting to enable passwordless connection from jenkins
```sh
vi /etc/ssh/sshd_config
```
Change the setting as per below

```diff
- Before 
```
![image](https://user-images.githubusercontent.com/111989928/198711105-0672a166-bf18-4922-a0dd-074bf7f75f8d.png)
```diff
+ After
```
![image](https://user-images.githubusercontent.com/111989928/198711366-3b7384d3-e18c-42c4-b966-149aeaefbcd6.png)

Edit and Save 

Restart the ssh service
```sh
service sshd restart
```


## 3. Now Setup the Jenkins and install below Plugins

`ssh2`
`Publish over ssh`
`ssh`
`Docker Pipeline`
`docker-build-step`
`CloudBees Docker Build and Publish plugin`
`Kuberenetes deploy-custom` Old version should be used (1.0) stored @ **[Kubernetes Plugin](https://github.com/Raam043/Sign_Up_-_Sign_In/blob/main/kubernetes-cd.hpi)**

https://github.com/Raam043/Sign_Up_-_Sign_In/raw/main/kubernetes-cd.hpi

Deploy plugin at advance setting

![image](https://user-images.githubusercontent.com/111989928/200244582-63b42e64-93da-47d3-88d2-887f995d9687.png)


Open Jenkins Application and 

Setup the connection for Kubernetes Master


![image](https://user-images.githubusercontent.com/111989928/200245147-069f0a9c-514d-4943-b65f-b972e26bc569.png)




Set DockerHub password as Environment variable @ Jenkins server to Push docker images to registry 
```sh
export PASS="DockerHub_Password"
```


# Option -1 for Pipeline = Create `Freestyle project`

Git = https://github.com/Raam043/Sign_Up_-_Sign_In.git

`Build Steps` Select = `Execute shell` Remove existing images

```sh
docker image rm -f raam043/login-project
docker image rm -f nginx
```
![image](https://user-images.githubusercontent.com/111989928/200275474-13c09fb9-570c-4325-b1d1-12388845aa18.png)





Select `Docker Build and Publish` to registry ( Add DockerHub credentials to push images)

![image](https://user-images.githubusercontent.com/111989928/200275901-a0b4ccbd-dcca-4966-b388-cf1fe72e65da.png)

![image](https://user-images.githubusercontent.com/111989928/200275925-43da8ada-ddc2-474b-8af2-daf3c61c07b7.png)



Select `Build Steps` = `Remote Shell` Stopping all existing resources 


![image](https://user-images.githubusercontent.com/111989928/200278952-c5e8e9f6-0246-4dda-a964-749ee0d76dcf.png)




Select `Build Steps` = `Deploy to Kubernetes`


Create Pod @ kubernetes Master

Add Credentials of Master to deploy services on Kubernetes cluster

to get the password of Master login on master server and run the below command to get the config details
```sh
cat .kube/config
```
Output
![image](https://user-images.githubusercontent.com/111989928/200277577-403fd191-56bc-467a-88d2-0303ed058e20.png)


Copy all Output and paste on Credentials for Master

![image](https://user-images.githubusercontent.com/111989928/200278321-96c79bc1-9104-434e-90a9-e025f2e2c474.png)




`Deploying Application @ Kubenetes Master`

![image](https://user-images.githubusercontent.com/111989928/200303498-7f6e8c92-ea8b-48d8-bec7-01989a26f1d8.png)






`Output`



![image](https://user-images.githubusercontent.com/111989928/200188753-d92aad4c-f660-47a3-89a9-7864702598ac.png)


![image](https://user-images.githubusercontent.com/111989928/200188737-177e2ba2-bd7e-40d4-b754-6b0c446a1e98.png)

# Option -2 for Pipeline = Create `Pipeline project`

Below is the Jenkinsfile Script

```sh
node {
  def remote = [:]
  remote.name = 'Master'
  remote.host = '18.188.57.198'
  remote.user = 'root'
  remote.password = 'ramesh123'
  remote.allowAnyHosts = true
  stage('SSH-Kube Cleanup - Old project records') {
    sshCommand remote: remote, command: "kubectl delete all --all"
  }
}
pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Raam043/Sign_Up_-_Sign_In.git'
            }
        }
        stage('Docker Image Build') {
            steps {
                sh 'docker image rm -f raam043/login-project & docker build -t raam043/login-project .'
            }
        }
        stage('Docker Image Push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'DP', variable: 'DP')]) {
                    sh 'docker login -u raam043 -p ${DP}'
                    sh 'docker push raam043/login-project'
            }
        }
        }
        stage('Deploying Application on Kubernetes') {
            steps {
                kubernetesDeploy configs: 'deployment.yml', kubeConfig: [path: ''], kubeconfigId: 'Kube-master-cnfg', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
            }
        }
        
    }    
}
```


![image](https://user-images.githubusercontent.com/111989928/200372184-32120361-8419-4f44-850a-5985915bd9ac.png)

