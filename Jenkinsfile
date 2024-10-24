pipeline{
    agent any
    tools {
     maven 'maven-home'
    } 
    
    environment {
        DOCKER_TAG = getVersion()
    }
   
    stages{
        stage('SCM'){
            steps{
                git credentialsId: '56180789-d710-43c5-85a5-3b4f21ee8836',
                url: 'https://github.com/Akerur19/dockeransiblejenkins.git'
            }
        }
    
   
        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build . -t akshaydocker19/demoapp:${DOCKER_TAG}'
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u akshaydocker19 -p ${dockerHubPwd}"
                }
                
                sh "docker push akshaydocker19/demoapp:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible-cd-test', inventory: 'dev.inv', playbook: 'deploy-docker.yml', vaultTmpPath: ''
            }
        }
    }
}

def getVersion()
{
def commitakshay=sh returnStdout: true, script: 'git rev-parse --short HEAD'
return commitakshay
}
