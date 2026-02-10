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
                    userRemoteConfigs: [[url: 'https://github.com/danvisrinivas/demo.git']]
                )
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy & Run on EC2') {
    steps {
        sh '''
        mkdir -p $APP_DIR
        cp $BUILD_JAR $APP_DIR/$JAR_NAME

        echo "================================="
        echo "Starting Spring Boot application"
        echo "================================="

        BUILD_ID=dontKillMe nohup java -jar $APP_DIR/$JAR_NAME > $APP_DIR/app.log 2>&1 &
        
        echo "Waiting for app to start..."
        sleep 15

        echo "Last 50 lines of app.log:"
        tail -50 $APP_DIR/app.log
        '''
    }
}

    }
}









