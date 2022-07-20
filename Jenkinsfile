pipeline {
    agent any
    tools {
      maven 'maven3'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages {
        stage('check pwd'){
            steps{
               sh "pwd"
            }
            
        }
        stage('SCM') {
            steps {
                git credentialsId: 'docker-ansible-https', 
                    url: 'https://github.com/bavuvan/dockeransiblejenkins.git'
            }
        }
        
        stage('Maven build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker build'){
            steps{
                sh "docker build . -t bavv/docker-ansible-demo:${DOCKER_TAG}"
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub-bavv-password', variable: 'dockerHubPassword')]) {
                    sh "docker login -u bavv -p ${dockerHubPassword}"
                }
                sh "docker push bavv/docker-ansible-demo:${DOCKER_TAG}"
            }
        }
        
        stage('Use ansible to deploy container on dev-server'){
            steps{
                ansiblePlaybook credentialsId: 'learn-devops-pem', disableHostKeyChecking: true, extras: '-e DOCKER_TAG=${DOCKER_TAG}', installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def result = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return result
}