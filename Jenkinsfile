pipeline {
    agent any

    tools {
        maven 'MAVEN'
    }

    environment {
        APP_DIR = "/opt/springboot-app"
        JAR_NAME = "app.jar"
        BUILD_JAR = "target/demo-0.0.2-SNAPSHOT.jar"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/danvisrinivas/demo.git'
                    ]]
                )
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    mkdir -p $APP_DIR
                    cp $BUILD_JAR $APP_DIR/$JAR_NAME
                '''
            }
        }

        stage('Run Spring Boot') {
            steps {
                sh '''
                    echo "Stopping old app if running..."
                    pkill -f "app.jar" || true

                    echo "Starting new app..."
                    nohup java -jar $APP_DIR/$JAR_NAME > $APP_DIR/app.log 2>&1 &
                '''
            }
        }
    }
}
