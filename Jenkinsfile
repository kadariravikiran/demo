pipeline {
  agent any

  tools {
    maven 'MAVEN'
  }

  environment {
    APP_DIR  = "/opt/springboot-app"
    JAR_NAME = "app.jar"
    PORT     = "9999"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scmGit(
          branches: [[name: '*/main']],
          userRemoteConfigs: [[url: 'https://github.com/danvisrinivas/demo.git']]
        )
        sh 'git log -1 --oneline'
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean package -DskipTests=true'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }

    stage('Deploy') {
      steps {
        sh """
          set -eux

          # copy latest jar (no hardcoded version)
          cp target/*.jar ${APP_DIR}/${JAR_NAME}

          # stop existing app on 9999
          PID=\$(sudo lsof -t -i:${PORT} || true)
          if [ -n "\$PID" ]; then
            sudo kill -9 \$PID
          fi

          # start new app
          nohup java -jar ${APP_DIR}/${JAR_NAME} > ${APP_DIR}/app.log 2>&1 &
          sleep 3

          # verify
          curl -f http://localhost:${PORT}/test
        """
      }
    }
  }
}
