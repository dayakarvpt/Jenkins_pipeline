pipeline {
    environment {
        imagename = "dayagarv/maven_docker"
        dockerimage = ""
        containername = 'my-container'
        dockerHubcredential = 'Jenkins'
    }

    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '9ffd4ee4-3647-4a7d-a357-5e8746463282', url: 'https://bitbucket.org/ananthkannan/myawesomeangularapprepo/']]])
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -f MyAwesomeApp/pom.xml clean install'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: '**/*.jar', allowEmptyArchive: true
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    docker.withServer('tcp://localhost:4243') {
                        docker.withRegistry('https://index.docker.io/v1/', 'fa32f95a-2d3e-4c7b-8f34-11bcc0191d70') {
                            def image = docker.build("ananthkannan/mywebapp", "MyAwesomeApp")
                            image.push()
                        }
                    }
                }
            }
        }

        stage('Docker Stop Container') {
            steps {
                sh 'docker ps -f name=myContainer -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -f name=myContainer -q | xargs -r docker container rm'
            }
        }

        stage('Docker Run') {
            steps {
                script {
                    docker.image("ananthkannan/mywebapp").run("-p 8085:8085 --rm --name myContainer")
                }
            }
        }
    }
}
