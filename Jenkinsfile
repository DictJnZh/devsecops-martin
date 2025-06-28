pipeline {
    agent any

    tools {
        maven 'mvn'       // Nom de Maven dans Jenkins
        jdk 'JDK21'       // Nom du JDK dans Jenkins
    }

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Id Jenkins des creds DockerHub
        DOCKER_IMAGE = 'directjnzh/devsecops-martin'  // Nom de l'image Docker mis à jour
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    def mvnHome = tool 'mvn'
                    sh "${mvnHome}/bin/mvn clean verify"
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        def mvnHome = tool 'mvn'
                        sh "${mvnHome}/bin/mvn sonar:sonar -Dsonar.projectKey=devsecops-martin -Dsonar.projectName='devsecops-martin'"
                    }
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        def image = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                        image.push()
                        image.push('latest')
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
