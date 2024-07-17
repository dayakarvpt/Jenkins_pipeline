pipeline {
    environment {
        imagename = "dayagarv/maven_docker"
        dockerImage = ''
        containerName = 'my-container'
        dockerHubCredentials = 'jenkins'
    }
 
    agent any
 
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/dayakarvpt/Jenkins_pipeline.git']]])
            }
        }
   
        stage('Build') {
            steps {
                script {
                    sh 'mvn -f pom.xml clean install'
                }
            }
        }
 
        stage('Building Image') {
            steps {
                script {
                    dockerImage = docker.build("${imagename}:latest", "MyAwesomeApp")
                }
            }
        }
 
        stage('Running Image') {
            steps {
                script {
                    sh "docker run -d --name ${containerName} ${imagename}:latest"
                    // Perform any additional steps needed while the container is running
                }
            }
        }
 
        stage('Stop and Remove Container') {
            steps {
                script {
                    sh "docker stop ${containerName} || true"
                    sh "docker rm ${containerName} || true"
                }
            }
        }
 
        stage('Deploy Image') {
            steps {
                script {
                    // Use Jenkins credentials for Docker Hub login
                    withCredentials([usernamePassword(credentialsId: dockerHubCredentials, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
 
                        // Push the image
                        sh "docker push ${imagename}:latest"
                    }
                }
            }
        }
    }
}
