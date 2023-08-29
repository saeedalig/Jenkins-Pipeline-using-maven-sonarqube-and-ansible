
## Jenkins Pipeline using maven sonarqube and ansible


```
pipeline {
     agent any

    }
    stages {

        stage('Git Checkout') {
            steps {
                script {
                    git branch: 'main',  // Change this to your desired branch
                        url: 'https://github.com/saeedalig/Kubernetes-Deployment-using-CICD-Pipeline.git'  // Change this to your repository URL
                }
            }
        }
    }
}
```
