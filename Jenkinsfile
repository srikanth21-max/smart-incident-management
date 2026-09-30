pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
    }

    environment {
        DOCKERHUB_USER = 'srikanth7349'
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/sim-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/sim-frontend"
        IMAGE_TAG      = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                bat "docker build -t %BACKEND_IMAGE%:%IMAGE_TAG% -t %BACKEND_IMAGE%:latest backend"
            }
        }

        stage('Build Frontend Image') {
            steps {
                bat "docker build --build-arg VITE_API_URL=http://localhost:8081 -t %FRONTEND_IMAGE%:%IMAGE_TAG% -t %FRONTEND_IMAGE%:latest frontend"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-sim',
                                                  usernameVariable: 'DH_USER',
                                                  passwordVariable: 'DH_PASS')]) {
                    bat 'echo %DH_PASS%| docker login -u %DH_USER% --password-stdin'
                    bat "docker push %BACKEND_IMAGE%:%IMAGE_TAG%"
                    bat "docker push %BACKEND_IMAGE%:latest"
                    bat "docker push %FRONTEND_IMAGE%:%IMAGE_TAG%"
                    bat "docker push %FRONTEND_IMAGE%:latest"
                }
            }
        }
    }

    post {
        always {
            bat 'docker logout'
        }
    }
}