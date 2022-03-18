pipeline{
    agent any
    tools {
      maven 'maven-3.8.4'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/manikumar9999/dockeransiblejenkins.git'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean install package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t manikumar99/maniapp:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub1', variable: 'dockerHubPwd')]) {
                    sh "docker login -u manikumar99 -p ${dockerHubPwd}"
                }
                
                sh "docker push manikumar99/maniapp:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'apache-server', disableHostKeyChecking: true, installation: 'ansible2', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
