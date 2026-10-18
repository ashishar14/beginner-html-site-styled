pipeline {
  agent { label 'k8s-agent' }
  triggers { githubPush() }
  environment {
    IMAGE = 'ashishar14/website:latest'
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build image') {
      steps { sh 'docker build -t $IMAGE .' }
    }
    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
          sh 'docker push $IMAGE'
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f deployment.yaml -f service.yaml'
        sh 'kubectl rollout restart deployment/website-deployment'
      }
    }
  }
}
