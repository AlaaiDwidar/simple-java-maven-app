pipeline {
    agent any
    tools {
     maven 'MAVEN3'
     }
    environment {
        
        DOCKER_REGISTRY = 'http://192.168.185.158:8082'
        DOCKER_IMAGE_NAME = "my-docker-registry:${BUILD_NUMBER}"
    }

    stages {

        stage ('SCM Import'){
            steps{
                    checkout scmGit(branches: [[name: '*/master']], 
                        extensions: [], 
                        userRemoteConfigs: [[url: 'https://github.com/AlaaiDwidar/simple-java-maven-app.git']])
        }
        }
        
        stage('Maven Build'){
            steps{
                    sh "mvn clean package -DskipTests=true"
                    archive 'target/*.jar'
                }
        }

        stage('Build Image') {
            steps {
                script{
                echo 'Building Image ...'
                sh "docker build -t 192.168.185.158:8082/app:${BUILD_NUMBER} ."
            }
            }
        }

        stage('Push Image to Nexus') {
            steps {
                script{
                echo 'Pushing image to Docker hosted repository on Nexus'
                withCredentials([usernamePassword(credentialsId: 'Nexus', passwordVariable: 'PSW', usernameVariable: 'USER')]) {
                    sh "docker login -u ${USER} -p ${PSW} 192.168.185.158:8082"
                    sh "docker push 192.168.185.158:8082/app:${BUILD_NUMBER}"
                    sh "docker image rm 192.168.185.158:8082/app:${BUILD_NUMBER}"
                }
                }
            }
        }
    }
}
