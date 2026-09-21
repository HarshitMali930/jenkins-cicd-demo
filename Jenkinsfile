pipeline {
    agent any

    environment {
        APP_NAME = 'jenkins-cicd-demo'
        IMAGE_TAG = 'latest'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'python3 -m py_compile app/app.py'
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests...'
                sh 'python3 -m pytest'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application...'
                sh 'tar -czf ${APP_NAME}.tar.gz app tests requirements.txt'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${APP_NAME}:${IMAGE_TAG} .'
            }
        }
    }
}
