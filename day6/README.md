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

### two stages

```
pipeline {
    agent any

    stages {
        stage('taking source code from git repo') {
            steps {
                echo 'cloning git repo..' // echo is predefine keyword in jenkinsfile 
                git branch: 'master',
                    url: 'https://github.com/redashu/cloud4c-jenkins-webapp.git' // automatically clone repo date
                // verify it 
                sh 'ls -a' // running linux command to check git data 
            }
        }
        stage('using docker-compose to build and run'){
            steps {
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
                sh  'docker-compose ps'
                sh  'docker-compose images'
            }
        }
    }
}

```

### with agent label jenkinsfile

```
pipeline {
    agent {
        label 'master' // manual scheduling
    }

    stages {
        stage('taking source code from git repo') {
            steps {
                echo 'cloning git repo..' // echo is predefine keyword in jenkinsfile 
                git branch: 'master',
                    url: 'https://github.com/redashu/cloud4c-jenkins-webapp.git' // automatically clone repo date
                // verify it 
                sh 'ls -a' // running linux command to check git data 
            }
        }
        stage('using docker-compose to build and run'){
            steps {
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
                sh  'docker-compose ps'
                sh  'docker-compose images'
            }
        }
    }
}

```

### adding testing stage

```
pipeline {
    agent {
        label 'master' // manual scheduling
    }

    stages {
        stage('taking source code from git repo') {
            steps {
                echo 'cloning git repo..' // echo is predefine keyword in jenkinsfile 
                git branch: 'master',
                    url: 'https://github.com/redashu/cloud4c-jenkins-webapp.git' // automatically clone repo date
                // verify it 
                sh 'ls -a' // running linux command to check git data 
            }
        }
        stage('using docker-compose to build and run'){
            steps {
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
                sh  'docker-compose ps'
                sh  'docker-compose images'
            }
        }
        // testing container status and checking health page output
        stage('testing'){
            steps {
                echo 'checking running container status'
                sh 'docker ps | grep -w Up | grep -w ashu-web-c12' // if container is running then no fail
                sh 'curl -f http://localhost:1235/health.html'
            }
        }
    }
}

```


