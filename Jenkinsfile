pipeline {
  agent any

  environment {
    IMAGE = 'faatyy/postshare-backend:1.0.0'
  }

  stages {

    stage('Maven build') {
      steps {
        dir('backend') {
          sh 'mvn -B clean package -DskipTests'
        }
      }
    }

    stage('Docker build') {
      steps {
        dir('backend') {
          sh 'docker build -t $IMAGE .'
        }
      }
    }

    stage('Docker push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub',
            usernameVariable: 'DH_USER', passwordVariable: 'DH_TOKEN')]) {
          sh 'echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin'
          sh 'docker push $IMAGE'
        }
      }
    }

    stage('Docker compose') {
      steps {
        sh 'docker compose up -d'
      }
    }
  }
}
