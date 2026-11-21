pipeline {
  agent any

  options {
    skipDefaultCheckout(true)
  }

  environment {
    BACK_IMAGE  = 'faatyy/postshare-backend:1.0.0'
    FRONT_IMAGE = 'faatyy/postshare-frontend:1.0.0'
  }

  stages {

    stage('Git') {
      steps {
        checkout scm
      }
    }

    stage('Maven build') {
      steps {
        dir('backend') {
          sh 'mvn -B clean package -DskipTests'
        }
      }
    }

    stage('Docker image') {
      steps {
        dir('backend') {
          sh 'docker build -t $BACK_IMAGE .'
        }
        dir('frontend') {
          sh 'docker build -t $FRONT_IMAGE .'
        }
      }
    }

    stage('Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub',
            usernameVariable: 'DH_USER', passwordVariable: 'DH_TOKEN')]) {
          sh 'echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin'
          sh 'docker push $BACK_IMAGE'
          sh 'docker push $FRONT_IMAGE'
        }
      }
    }

    stage('Docker-compose') {
      steps {
        sh 'docker compose up -d'
      }
    }
  }
}
