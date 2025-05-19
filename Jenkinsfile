pipeline {
    agent any

    environment {
        IMAGE_NAME = 'munevvernure/devops4'
        DOCKER_CREDS = credentials('dockerhub-creds-id')
    }

    stages {
        stage('Clone from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/munevvernure/DevOps4.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Apply Deployment to K8s') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }

        stage('Apply Service to K8s') {
            steps {
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}
