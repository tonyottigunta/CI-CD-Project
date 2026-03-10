pipeline {
  agent any

  environment {
    DOCKER_USER = "tonyor05"
    IMAGE_NAME  = "cicd-demo"
  }

  triggers {
    githubPush()
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/tonyottigunta/CI-CD-Project.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_USER/$IMAGE_NAME:latest .'
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'echo $PASS | docker login -u $USER --password-stdin'
          sh 'docker push $DOCKER_USER/$IMAGE_NAME:latest'
          sh 'docker logout'
        }
      }
    }

    stage('Deploy on EC2') {
      steps {
        sh '''
          docker stop demo-container || true
          docker rm demo-container || true
          docker pull $DOCKER_USER/$IMAGE_NAME:latest
          docker run -d \
            -p 3000:3000 \
            --name demo-container \
            --restart unless-stopped \
            $DOCKER_USER/$IMAGE_NAME:latest
        '''
      }
    }

    stage('Verify') {
      steps {
        sh 'sleep 3'
        sh 'curl -f http://localhost:3000 || echo "App not responding"'
      }
    }

  }

  post {
    success {
      echo '✅ Pipeline SUCCESS — app deployed!'
    }
    failure {
      echo '❌ Pipeline FAILED — check logs above'
    }
    always {
      sh 'docker image prune -f'
    }
  }

}
