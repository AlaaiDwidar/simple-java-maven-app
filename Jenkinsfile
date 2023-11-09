pipeline {
    agent any
    tools {
     maven 'MAVEN3'
     }
    environment {
        
        DOCKER_REGISTRY = '192.168.185.204:8083'
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
                script{
                echo 'Building Image ...'
                sh " docker build -t 192.168.185.204:8083/app:${BUILD_NUMBER} ."  
            }
            }
        }

        stage('Push Image to Nexus') {
            steps {
                script{
                echo 'Pushing image to Docker hosted repository on Nexus'
                withCredentials([usernamePassword(credentialsId: 'nexus', passwordVariable: 'PSW', usernameVariable: 'USER')]) {
                    sh " podman login -u ${USER} -p ${PSW} 192.168.185.204:8083 --tls-verify=false" 
                    sh " docker images"
                    sh " docker push http://192.168.185.204:8083/app:${BUILD_NUMBER} "
                    sh " docker image rm 192.168.185.204:8083/app:${BUILD_NUMBER}" 
                }
                }
            }
        }
    }
}
