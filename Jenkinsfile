pipeline {
    agent any
     tools {
     maven 'MAVEN3'
     }
    environment {
        
        DOCKER_REGISTRY = 'http://192.168.185.158:8082/repository/my-docker-registry'
        DOCKER_IMAGE_NAME = "app:${BUILD_NUMBER}"
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
                echo 'Building Image ...'
                sh "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME} ."
            }
        }

        stage('Push Image to Nexus') {
            steps {
                echo 'Pushing image to Docker hosted repository on Nexus'
                withCredentials([usernamePassword(credentialsId: 'Nexus', passwordVariable: 'PSW', usernameVariable: 'USER')]) {
                    sh "docker login -u ${USER} -p ${PSW} 192.168.185.158:8082"
                    sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}"
                    sh "docker image rm ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}"
                }
            }
        }
    }
}
