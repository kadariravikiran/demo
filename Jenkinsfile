pipeline {
  agent any

  tools {
    maven 'MAVEN'
  }

  environment {
    APP_DIR = "/opt/springboot-app"
    JAR_NAME = "app.jar"
  }

  stages {

    stage('Checkout') {
      steps {
        // Use the SAME repo that triggered the build (your repo configured in Jenkins job)
        checkout scm
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
        sh '''
          set -e

          # pick the actual built jar (ignore *.original)
          BUILD_JAR=$(ls -1 target/*.jar | grep -v "\\.original$" | head -n 1)

          echo "Built jar: $BUILD_JAR"
          echo "Deploying to: ${APP_DIR}/${JAR_NAME}"

          sudo mkdir -p "${APP_DIR}"
          sudo cp "$BUILD_JAR" "${APP_DIR}/${JAR_NAME}"

          # restart the app (quick method)
          sudo pkill -f "${APP_DIR}/${JAR_NAME}" || true
          nohup java -jar "${APP_DIR}/${JAR_NAME}" > "${APP_DIR}/app.log" 2>&1 &

          sleep 2
          echo "Health check:"
          curl -sSf http://localhost:8080/test | head -c 200; echo
        '''
      }
    }
  }
}
