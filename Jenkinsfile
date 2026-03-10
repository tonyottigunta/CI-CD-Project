pipeline {
agent any

environment {
DOCKER_USER = "tonyor05"
IMAGE_NAME = "cicd-demo"
}

  triggers {
    githubPush()
  }

stages {

```
stage('Checkout') {
  steps {
    git 'https://github.com/tonyottigunta/CI-CD-Project.git'
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
    }
  }
}

stage('Deploy on EC2') {
  steps {
    sh '''
    docker stop demo-container || true
    docker rm demo-container || true
    docker pull $DOCKER_USER/$IMAGE_NAME:latest
    docker run -d -p 3000:3000 --name demo-container $DOCKER_USER/$IMAGE_NAME:latest
    '''
  }
}
```

}
}
