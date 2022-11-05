pipeline{
    agent any
    tools {
      maven 'maven3'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
  
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/kose1n/ans-jenk'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t kammana/hariapp:${DOCKER_TAG} "
            }
        }
        
      stage("dockerhub login") {
            steps {
                echo " = docker login and push ="
                withCredentials([usernamePassword(credentialsId: 'dockerhub_kose1n', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh "docker login -u $USERNAME -p $PASSWORD"
                }
            }
        }
        stage("dockerhub push") {
            steps {
                echo " = start pushing image ="
                sh '''
                docker push kose1n/imagetool:${DOCKER_TAG}
                '''
            }
        }
        //s
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: '#', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'hosts.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
