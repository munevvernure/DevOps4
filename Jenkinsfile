pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKER_IMAGE = "munevvernure/devops4"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Clone Repository') {
            steps {
            git branch: 'main', url: 'https://github.com/munevvernure/DevOps4.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }

        stage('Deploy to Kubernetes (Deployment)') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }

        stage('Deploy to Kubernetes (Service)') {
            steps {
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
        stage('Restart Deployment') {
                    steps {
                        sh 'kubectl rollout restart deployment devops4-deployment'
                    }
                }

    }
}
