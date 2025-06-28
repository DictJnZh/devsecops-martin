pipeline {
    agent any

    tools {
        maven 'mvn'
        jdk 'JDK21'
    }

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKER_IMAGE = 'directjnzh/devsecops-martin'
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
                    sh "${mvnHome}/bin/mvn clean package -DskipTests"
                }
            }
        }

        stage('Generate Dockerfile') {
            steps {
                script {
                    writeFile file: 'Dockerfile', text: '''
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
                    '''.stripIndent()
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
