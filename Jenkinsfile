pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk   'JDK11'
    }

    environment {
        TOMCAT_URL = 'http://<your-tomcat-ip>:8080'
        APP_NAME   = 'java-tomcat-app'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/<your-username>/java-tomcat-app.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'tomcat-credentials',
                        path: "/${APP_NAME}",
                        url: "${TOMCAT_URL}"
                    )
                ],
                contextPath: "/${APP_NAME}",
                war: 'target/*.war'
            }
        }
    }

    post {
        success {
            echo "✅ Deployed → ${TOMCAT_URL}/${APP_NAME}"
        }
        failure {
            echo "❌ Build failed — check the logs above"
        }
    }
}
