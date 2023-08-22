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

### testing jenkinsfile way to build docker image 

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
        // using jenkinsfile inbuilt method to build docker image
        stage('building image using jenkinsfile way'){
            steps {
                echo 'lets use jenkinsfile way to build image'
                script {
                    def imageName = "ashu-cloud4c-app" // def to create variables 
                    def imageTag  = "version$BUILD_NUMBER"
                    // using docker build plugin 
                    docker.build(imageName + ":" + imageTag, "-f Dockerfile . ")
                }
                // verify that 
                sh 'docker images | grep ashu-cloud4c-app'
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

### jenkinsfile with docker hub push 

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
        // using jenkinsfile inbuilt method to build docker image
        stage('building image using jenkinsfile way'){
            steps {
                echo 'lets use jenkinsfile way to build image'
                script {
                    def imageName = "dockerashu/ashu-cloud4c-app" // def to create variables 
                    def imageTag  = "version$BUILD_NUMBER"
                    // using docker build plugin 
                    docker.build(imageName + ":" + imageTag, "-f Dockerfile . ")
                }
                // verify that 
                sh 'docker images | grep ashu-cloud4c-app'
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
        // pushing image to docker hub 
        stage('image pushing'){
            steps {
                echo 'defining docker image and registry cred'
                script {
                    def imageName = "dockerashu/ashu-cloud4c-app" // def to create variables 
                    def imageTag  = "version$BUILD_NUMBER"
                    def dockerCred = "0e48fab8-5398-462a-be9f-599efd974c4a"
                    // loging to docker hub
                    docker.withRegistry('https://registry.hub.docker.com',dockerCred){
                        // pushing image
                        docker.image(imageName + ":" + imageTag ).push()
                    }
                    
                    
                }
            }
        }
    }
}

```

