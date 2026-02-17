


pipeline {
    agent any

    tools {
        maven 'MAVEN'
    }

    environment {
        APP_DIR   = "/opt/springboot-app"
        JAR_NAME  = "app.jar"
        BUILD_JAR = "target/demo-0.0.2-SNAPSHOT.jar"

        DOCKERHUB_USER = "ravikirankadari"
        DOCKERHUB_PASS = "Ravi@1234"

        DOCKERHUB_REPO = "ravikirankadari/spring-io:trysol"

        IMAGE_TAG    = "${BUILD_NUMBER}"
        IMAGE_FULL   = "${DOCKERHUB_REPO}:${IMAGE_TAG}"
        IMAGE_LATEST = "${DOCKERHUB_REPO}:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/kadariravikiran/demo.git']]
                )
            }
        }

        stage('Build maven project') {
            steps {
                sh "mvn clean install -DskipTests=true"
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                  set -eux
                  test -f ${BUILD_JAR}
                  docker build --build-arg JAR_FILE=${BUILD_JAR} \
                  -t ${IMAGE_FULL} \
                  -t ${IMAGE_LATEST} .
                """
            }
        }

        stage('Docker Login & Push') {
            steps {
                sh """
                  echo "${DOCKERHUB_PASS}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                  docker push ${IMAGE_FULL}
                  docker push ${IMAGE_LATEST}
                  docker logout
                """
            }
        }
    }
}

