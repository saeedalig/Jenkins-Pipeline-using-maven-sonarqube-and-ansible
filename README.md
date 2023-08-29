
## Jenkins Pipeline using maven sonarqube and ansible



#### Git Checkout
```
pipeline {
     agent any

    }
    stages {

        stage('Git Checkout') {
            steps {
                script {
                    git branch: 'main',  // Change this to your desired branch
                        url: 'https://github.com/saeedalig/Jenkins-Pipeline-using-maven-sonarqube-and-ansible.git'  // Change this to your repository URL
                }
            }
        }
    }
}
```

#### Maven Package: Maven
```
stage('Maven Package') {
            steps {
                script {
                   sh 'mvn clean package'
                }
            }
        }
```

#### Code Analysis: SonarQube
```
stage('Code Analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar') {
                         sh 'mvn sonar:sonar'
                    } 
                }
            }
        }
```

#### Docker Image: Docker
```
stage('Build Docker nImage') {
            steps {
                script {
                   sh "docker image build -t ${JOB_NAME}:v1.${BUILD_ID} ."
                   sh "docker image tag  ${JOB_NAME}:v1.${BUILD_ID} asa96/${JOB_NAME}:v1.${BUILD_ID}"
                   sh "docker image tag  ${JOB_NAME}:v1.${BUILD_ID} asa96/${JOB_NAME}:latest"
                }
            }
        }
```

#### Docker Push: Dockerhub
```
stage('Build Docker nImage') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'dockerhub', variable: 'DOCKER_CREDS']) {
                         sh "docker login -u asa96 -p ${DOCKER_CREDS}"
                         sh "docker image push  asa96/${JOB_NAME}:v1.${BUILD_ID}"
                         sh "docker image push  asa96/${JOB_NAME}:latest"
                         // Clean up the space once images are pushed
                         sh "docker rmi ${JOB_NAME}:v1.${BUILD_ID} asa96/${JOB_NAME}:v1.${BUILD_ID} asa96/${JOB_NAME}:latest"
                    } 
                }
            }
        }
```

#### Run Container on Ansible Server 
```
stage('Run Container') {
            steps {
                script {
                   sshagent ([ansible-server]) {
                         sh "ssh -o StrictHostKeyCheking=no ubuntu@172.31.80.112 docker run -d -p 8000:80 --name static-webpage asa96/static-webpase-img"
                    } 
                }
            }
        }
```




