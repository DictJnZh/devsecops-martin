pipeline {
    agent any

    tools {
        maven 'mvn'       // ici le nom de Maven configuré dans Jenkins
        jdk 'JDK21'       // nom du JDK configuré
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
    }

    post {
        always {
            cleanWs()
        }
    }
}
