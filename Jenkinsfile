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
        stage('depoly'){
  steps{
    sh '''
      set -eux

      # copy latest jar
      cp target/*.jar /opt/springboot-app/app.jar

      # stop existing app on 9999
      PID=$(sudo lsof -t -i:9999 || true)
      if [ -n "$PID" ]; then
        sudo kill -9 $PID
      fi

      # start new app from the correct path
      nohup java -jar /opt/springboot-app/app.jar > /opt/springboot-app/app.log 2>&1 &
      sleep 3

      # verify new app
      curl -f http://localhost:9999/test
    '''
  }
}

    }
}


