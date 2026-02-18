pipeline {
    agent any

    tools {
        maven 'MAVEN'
    }

    environment {
        APP_DIR = "/opt/springboot-app"
        JAR_NAME = "app.jar"
        BUILD_JAR = "target/demo-0.0.2-SNAPSHOT.jar"
        IMAGE = "ravikirankadari/springboot-io"
        DOCKERHUB_CREDS = credentials('dockerhub')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean install"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE} ."
            }
        }

        stage('Docker Run') {
            steps {
                sh "docker rm -f demo || true"
                sh "docker run -d --name demo -p 8081:9999 ${IMAGE}"
            }
        }

        stage('Docker Login') {
            steps {
                sh """
                echo ${DOCKERHUB_CREDS_PSW} | docker login -u ${DOCKERHUB_CREDS_USR} --password-stdin
                """
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${IMAGE}"
            }
        }

        stage('Deploy') {
            steps {
                sh "cp ${BUILD_JAR} ${APP_DIR}/${JAR_NAME}"
            }
        }
    }
}

