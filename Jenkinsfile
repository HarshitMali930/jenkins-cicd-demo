pipeline {

    agent any

    environment {
        APP_NAME = 'jenkins-cicd-demo'
        IMAGE_TAG = 'latest'

        DOCKERHUB_USERNAME = 'dockharry'
        DOCKER_IMAGE = 'dockharry/jenkins-cicd-demo'
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

                sh '''
                    python3 -m py_compile app/app.py
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Installing dependencies and running tests...'

                sh '''
                    rm -rf .venv
                    python3 -m venv .venv
                    .venv/bin/python -m pip install --upgrade pip
                    .venv/bin/python -m pip install -r requirements.txt
                    .venv/bin/python -m pytest -v
                '''
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application...'

                sh '''
                    tar -czf ${APP_NAME}.tar.gz app tests requirements.txt
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build -t ${APP_NAME}:${IMAGE_TAG} .
                    docker tag ${APP_NAME}:${IMAGE_TAG} ${DOCKER_IMAGE}:${IMAGE_TAG}
                '''
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'

                withCredentials([
                    string(
                        credentialsId: 'dockerhub-token',
                        variable: 'DOCKER_TOKEN'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_TOKEN" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                        docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                        docker logout
                    '''
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                echo 'Deploying application to Kubernetes...'

                sh '''
                    kubectl set image deployment/jenkins-cicd-demo \
                    app=${DOCKER_IMAGE}:${IMAGE_TAG}

                    kubectl rollout status deployment/jenkins-cicd-demo --timeout=120s
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying Kubernetes deployment...'

                sh '''
                    kubectl get deployment jenkins-cicd-demo
                    kubectl get pods -l app=jenkins-cicd-demo
                '''
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
