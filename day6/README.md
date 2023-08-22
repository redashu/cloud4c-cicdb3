# cloud4c-cicdb3

## Jenkinsfile 

### with Single stage 

```
pipeline {
    agent any

    stages {
        stage('taking source code from git repo') {
            steps {
                echo 'cloning git repo..' // echo is predefine keyword in jenkinsfile 
                git 'https://github.com/redashu/cloud4c-jenkins-webapp.git' // automatically clone repo date
                // verify it 
                sh 'ls -a' // running linux command to check git data 
            }
        }
    }
}

```


