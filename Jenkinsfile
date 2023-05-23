pipeline {

    agent {
        label 'mygroupagent'
    }

    tools{
        nodejs 'node'
        dockerTool 'docker'
    }
    triggers{
        githubPush()
    }

   environment {
      DOCKERHUB_CREDENTIALS = credentials('docker_id')
      IMAGE_NAME = 'stkraych/mynodejsapp'
    }

    stages {

        stage('Clean') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/stkraych/nodejs-my-proj.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm ci' //This is for building the nodejs project
            }
        }
        stage('Test') {
            steps {
                sh 'npm test' //This is for testing the nodejs modules
            }
        }
        stage('Deploy') {
           steps {
                 sh 'pkill node | true'
                sh 'npm install -g forever'
                sh 'forever start src/index.js'
           }
        }


       stage('Docker login'){
           steps {
               sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
	   }
        }

        stage('Docker build and tag'){
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }

	stage('Docker Push'){
            steps {
              sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
    stage('Docker Deploy'){
        steps{
            sh 'docker container rm -f mynodejsapp || true'
            sh 'docker container run -d -p 3000:3000 --name mynodejsapp ivanpiyvikov/mynodejsapp'
        }
    }
          
    }
   
 }
