pipeline {
  agent any
  environment {
    IMAGE = 'ameni050/fleetman-webapp'
    TAG   = "${env.BUILD_NUMBER}"
  }
  stages {
    stage('Build image') {
      steps { sh 'docker build -t $IMAGE:$TAG .' }
    }
    stage('Push image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
          sh 'docker push $IMAGE:$TAG'
        }
      }
    }
    stage('Deploy') {
      steps {
        sh 'kubectl apply -f manifests/services.yaml'
        sh 'kubectl set image deployment/webapp webapp=$IMAGE:$TAG'
        sh 'kubectl rollout status deployment/webapp --timeout=900s'
      }
    }
  }
}
