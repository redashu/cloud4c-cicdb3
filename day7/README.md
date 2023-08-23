# cloud4c-cicdb3

## Devops --> Devsecops 

<img src="sec.png">

### security layers in pipeline

<img src="sec1.png">

## sample jenkinsfile with custom stage agent 

```
pipeline {
    agent none

    stages {
        stage('Hello') {
            agent {
                label 'slave1-docker'
            }
            steps {
                echo 'Hello World'
                sh 'hostname -i'
            }
        }
        stage('hii'){
            agent {
                label 'master'
            }
            steps {
                echo 'my job is running in master system'
                sh 'date'
            }
        }
    }
}

```

### Code level security understnading 

<img src="sec2.png">

### adding prebuild security in jenkinsfile

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
        // Doing prebuild security check 
        stage('prebuild security check using trufflehog'){
            steps {
                echo 'scaning github URL for security scaning'
                sh 'docker run --rm  trufflesecurity/trufflehog:latest github --repo https://github.com/redashu/cloud4c-jenkins-webapp.git >/tmp/ashu-keycheck.txt'
                sh 'grep PRIVATE /tmp/ashu-keycheck.txt  && exit 1 '
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

### adding post build action 

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
        // Doing prebuild security check 
        stage('prebuild security check using trufflehog'){
            steps {
                echo 'scaning github URL for security scaning'
                sh 'docker run --rm  trufflesecurity/trufflehog:latest github --repo https://github.com/redashu/cloud4c-jenkins-webapp.git >/tmp/ashu-keycheck.txt'
                sh 'grep PRIVATE /tmp/ashu-keycheck.txt  && exit 1 '
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
    // creating post build action 
    post {
        success {
            echo 'sending email to all the teams '
            // code here to send
        }
        failure {
            echo 'checks logs '
            echo 'sending email to Dev or Sec teams'
        }
        always {
            echo 'keep checking for best security'
            
        }
    }
}

```

### pre and post build security testing 

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
        // Doing prebuild security check 
        stage('prebuild security check using trufflehog'){
            steps {
                echo 'scaning github URL for security scaning'
                sh 'docker run --rm  trufflesecurity/trufflehog:latest github --repo https://github.com/redashu/cloud4c-jenkins-webapp.git >/tmp/ashu-keycheck.txt'
                sh 'grep PRIVATE /tmp/ashu-keycheck.txt '
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
            agent {
                label 'helloslave2'
            }
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
        // post build security scan 
        stage('using post build to do security scan'){
            agent {
                label 'helloslave2' // here only we have trivy installed 
            }
            steps {
                echo 'lets scan for vulnearbility in above build image'
                sh 'docker save -o ui-project.tar dockerashu/ashu-cloud4c-app:version$BUILD_NUMBER'
                sh 'trivy image --input ui-project.tar --severity HIGH,CRITICAL  >/tmp/ashutest.txt'
                sh 'grep -i High /tmp/ashutest.txt'
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
            agent {
                label 'helloslave2'
            }
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
    // creating post build action 
    post {
        success {
            echo 'sending email to all the teams '
            // code here to send
        }
        failure {
            echo 'checks logs '
            echo 'sending email to Dev or Sec teams'
        }
        always {
            echo 'keep checking for best security'
            
        }
    }
}

```
