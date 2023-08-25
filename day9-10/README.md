# cloud4c-cicdb3

### CICD is all about planing and testing your pipeline 

<img src="plan.png">

### rolling updates 

<img src="roll.png">

### jenkinsfile 

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
                 git branch: 'main', url: 'https://github.com/redashu/cloud4c-jenkins-webapp.git'
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
            // now adding CD part to test connection of apiserver
        stage('testing k8s apiServer connection'){
            agent any 
            steps {
                echo 'sending Rest API request to the k8s master node'
                sh 'kubectl get nodes'
            }
        
        }
        // adding k8s deploy
        stage('sending request'){
            agent any 
            steps {
                echo 'using git repo and kubectl to deploy'
                git branch: 'master',
                    url: 'https://github.com/redashu/cloud4c-jenkins-webapp.git'
                    
                sh 'kubectl apply -f k8s-deploy/deploy-app.yaml && sleep 2'
                sh 'kubectl get deploy,pod,svc -n ashu-jk-deploy'
            }
        }
        // scale and upgrade
        stage('scale and upgrade using rolling updates'){
            steps {
                echo 'manual horizental scaling '
                sh 'kubectl scale deployment ashu-webapp --replicas 3 -n ashu-jk-deploy'
                echo 'updating new docker image in existing deployment'
                //                               name_of_deploy   name_of_container
                sh 'kubectl set image deployment ashu-webapp ashu-cloud4c-app=dockerashu/ashu-cloud4c-app:version$BUILD_NUMBER -n ashu-jk-deploy'
                echo 'checking rolling updates'
                sh 'kubectl rollout status deployment  ashu-webapp -n  ashu-jk-deploy'
                echo 'checking pod status'
                sh 'kubectl get pods -n ashu-jk-deploy'
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


