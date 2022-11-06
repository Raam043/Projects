# Private DockerHub repository CICD-Jenkins Script.

```sh

pipeline {
    agent any

    stages {
        stage('Docker pull from Hub with login') {
            steps {
                withCredentials([string(credentialsId: 'DP', variable: 'DP')]) {
                    sh 'docker login -u raam043 -p ${DP}'
                    sh 'docker stop myapp & docker rm -f myapp & docker image rm -f raam043/herat-animation & docker pull raam043/heart-animation'
            }
            }
        }
        stage('Docker container run') {
            steps {
                sh 'docker run --name myapp -d -p 443:80 raam043/heart-animation'
            }
        }
    }
}

````

![image](https://user-images.githubusercontent.com/111989928/200161283-63fd5007-bf12-470a-95b0-5473fd19cfe9.png)



![image](https://user-images.githubusercontent.com/111989928/200161228-58a45fd0-e28e-400c-a1fa-8ebcbc238b49.png)
