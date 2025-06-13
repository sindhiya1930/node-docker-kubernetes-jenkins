pipeline {
  agent any

  environment {
    DOCKERHUB_REGISTRY = 'sindhiya1930/demo-jenkins'
    DOCKERHUB_CREDENTIALS_ID = 'dockerhub'
  }

  stages {
    // stage('Install dependencies') {
    //   steps {
    //     sh 'npm install'
    //   }
    // }

    // stage('Test') {
    //   steps {
    //     sh 'npm test'
    //   }
    // }

    stage('Build Docker image') {
      steps {
        script {
          sh 'docker build -t ${DOCKERHUB_REGISTRY}:latest .'
        }
      }
    }

    stage('Push Docker image') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: DOCKERHUB_CREDENTIALS_ID,
          passwordVariable: 'DOCKERHUB_PASSWORD',
          usernameVariable: 'DOCKERHUB_USERNAME'
        )]) {
          sh 'docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD}'
          sh 'docker push ${DOCKERHUB_REGISTRY}:latest'
        }
      }
    }

    stage('Deploy to kubernates') {
      steps {
        sh '''
        aws eks update-kubeconfig --name skillfyme1
        kubectl apply -f node-web-app-deployment.yaml
        kubectl apply -f node-web-app-service.yaml
        '''
      }
    }
  }

  post {
    always {
      sh 'docker logout'
    }
  }
}
