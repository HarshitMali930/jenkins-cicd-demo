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
                checkout scm
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
                echo 'Installing Python dependencies...'
                sh 'python3 -m venv .venv'
                sh '.venv/bin/pip install --upgrade pip'
                sh '.venv/bin/pip install -r requirements.txt'

                echo 'Running automated tests...'
                sh '.venv/bin/pytest'
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

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check the console output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
