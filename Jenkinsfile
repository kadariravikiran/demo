pipeline{
    agent any
    tools{
        maven 'MAVEN'
    }
    environment{
    APP_DIR="/opt/springboot-app"
    JAR_NAME="app.jar"
    BUILD_JAR="target/demo-0.0.2-SNAPSHOT.jar"
    }
    stages{
        stage('Checkout'){
            steps{
               checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/danvisrinivas/demo.git']])
        }
        }
        stage('Build maven project'){
            steps{
                sh "mvn clean install -DskipTests=true"
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }
        stage('depoly'){
            steps{
                script{
                    sh 'cp $BUILD_JAR $APP_DIR/$JAR_NAME'
    }
}
        }
    }
}






