pipeline {
  agent any

  environment {
    IMAGE = "docker.io/yasserbelhajali/spring-demo"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build JAR') {
      steps {
        sh 'chmod +x mvnw || true'
        sh './mvnw -DskipTests clean package'
      }
    }

    stage('Build & Push Image') {
      steps {
        script {
          def sha = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          def tag = "${sha}-${env.BUILD_NUMBER}"

          withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                            usernameVariable: 'DH_USER',
                                            passwordVariable: 'DH_TOKEN')]) {
            sh """
              echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin
              docker build -t ${IMAGE}:${tag} .
              docker push ${IMAGE}:${tag}
            """
          }

          echo "Pushed: ${IMAGE}:${tag}"
        }
      }
    }
  }
}
