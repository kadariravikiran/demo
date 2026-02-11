pipeline {
  agent any

  tools {
    maven 'MAVEN'
  }

  environment {
    APP_DIR   = "/opt/springboot-app"
    JAR_NAME  = "app.jar"
    PORT      = "8080"
  }

  stages {

    stage('Checkout') {
      steps {
        deleteDir()
        checkout scmGit(
          branches: [[name: '*/main']],
          userRemoteConfigs: [[url: 'https://github.com/danvisrinivas/demo.git']]
        )
        sh 'git log -1 --oneline'
      }
    }

    stage('Build & Test') {
      steps {
        sh 'mvn -U clean test package'
        sh 'ls -lh target'
      }
    }

    stage('Deploy') {
      steps {
        sh """
          set -euxo pipefail

          # pick the jar that was just built (avoid hardcoding version)
          BUILD_JAR=\$(ls -1 target/*.jar | head -n 1)

          sudo mkdir -p ${APP_DIR}
          sudo cp "\$BUILD_JAR" ${APP_DIR}/${JAR_NAME}
          sudo chmod 755 ${APP_DIR}/${JAR_NAME}

          # stop old app if something is listening on 8080
          PID=\$(sudo lsof -t -i:${PORT} || true)
          if [ -n "\$PID" ]; then
            echo "Stopping old process \$PID"
            sudo kill -9 "\$PID"
          fi

          # start new app
          echo "Starting new app..."
          nohup java -jar ${APP_DIR}/${JAR_NAME} > ${APP_DIR}/app.log 2>&1 &

          # health check
          sleep 3
          curl -f http://localhost:${PORT}/test

          echo "Deploy completed."
        """
      }
    }
  }

  post {
    failure {
      echo "Pipeline failed. Check console logs and /opt/springboot-app/app.log on the server."
    }
  }
}
