pipeline {
  agent any
  environment{
             DOCKERHUB_CREDENTIALS=credentials('dockerhub')
         }

  stages {
    stage('Build') {
      steps {
        sh '''#!/bin/bash
        echo '******************* START TESTING *********************'
        sudo su - jenkins
        sudo apt-get update
        echo "installing Docker"
        sudo apt-get install docker.io -y
        echo "adding jenkins user to docker group"
        sudo groupadd docker
        sudo usermod -aG docker $USER
        newgrp docker
        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker login
        docker build -t tanveermeman/mainline.nginx.1.19.10.alpine:$BUILD_NUMBER .
        '''
        // Build the Docker image
        //sh 'docker build -t tanveermeman/mainline.nginx.1.19.10.alpine:$BUILD_NUMBER .'
      }
    }
    stage('Push to Registry') {
      steps {
        // Push the Docker image to a registry, using the Jenkins build number as the version
        sh 'docker push tanveermeman/mainline.nginx.1.19.10.alpine:$BUILD_NUMBER'
      }
    }
    stage('Deploy') {
      steps {
        // Deploy the Docker image to a Kubernetes cluster
        sh 'kubectl apply -f nginx-statefulset.yaml'
      }
    }
  }
}
