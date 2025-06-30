pipeline {
  agent none
  stages {
      stage ('git-clone'){
          agent any
          steps {
              git 'https://github.com/yogeshpenumur/Jenkins-pipelines-examples'
          }
      }
    stage('Back-end') {
      agent {
        docker { image 'maven:3.8.1-adoptopenjdk-11' }
      }
      steps {
        sh 'mvn clean package'
      }
    }
    stage('Create the image') {
        agent any
            steps {
                echo 'Create the image'
				sh "docker build . -t yogeshpenumur/img:${BUILD_NUMBER}"
            }
        }
    stage('push-docker'){
    agent any
    steps {
        withCredentials([string(credentialsId: 'docker-cr', variable: 'DOCKER_PASS')]) {
            sh "docker login -u yogeshpenumur2002@gmail.com -p ${DOCKER_PASS}"
            sh "docker push yogeshpenumur/img:${BUILD_NUMBER}"
        }
    }
}
        stage('Create the container') {
            agent any
            steps {
                echo 'Create the container'
		    sh "docker rm -f mycontainer"		
		    sh "docker run -d -p 9990:8080 --name mycontainer yogeshpenumur/img:${BUILD_NUMBER}"
            }
        }
        stage ('webapps '){
            agent any 
            steps{
                sh '''#!/bin/bash

# Set your container name
CONTAINER_NAME="mycontainer"

# Check if the container is running
if ! docker ps --format \'{{.Names}}\' | grep -q "^${CONTAINER_NAME}$"; then
  echo "❌ Container \'${CONTAINER_NAME}\' is not running."
  exit 1
fi

echo "✅ Copying webapps.dist contents to webapps inside \'${CONTAINER_NAME}\'..."

# Run the copy command inside the container
docker exec "${CONTAINER_NAME}" bash -c "cp -r /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps/"

if [ $? -eq 0 ]; then
  echo "✅ Successfully copied webapps.dist to webapps."
else
  echo "❌ Failed to copy files."
fi
'''



                
            }
        }
    
  }
}
