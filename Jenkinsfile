pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'nouresprit'
        COMPOSE_PROJECT_NAME = 'devops-appgestiondesprojets'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build images') {
            steps {
                sh 'docker build -t $DOCKERHUB_USER/backend-app:latest ./backend'
                sh 'docker build -t $DOCKERHUB_USER/frontend-app:latest ./frontend'
            }
        }

        stage('Push images') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_PASS'
                    )
                ]) {
                    sh 'echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin'
                    sh 'docker push $DOCKERHUB_USER/backend-app:latest'
                    sh 'docker push $DOCKERHUB_USER/frontend-app:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose -p $COMPOSE_PROJECT_NAME down || true'
                sh 'docker compose -p $COMPOSE_PROJECT_NAME up -d --build'
            }
        }
    }
}
