pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "yuvan2op"
        IMAGE_NAME = "${DOCKERHUB_USER}/basic-app"
        REPO_URL = "https://github.com/yuvan2op/jen.git"
    }

    stages {

        stage('Checkout') {
            steps {
                sh """
                    rm -rf repo || true
                    git clone ${REPO_URL} repo
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    cd repo/app
                    docker build -t ${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u "$USER" --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh """
                    docker push ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy Container') {
            steps {
                sh """
                    docker rm -f basic-app || true
                    docker pull ${IMAGE_NAME}:latest
                    docker run -d --name basic-app ${IMAGE_NAME}:latest
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline Finished!"
        }
    }
}
