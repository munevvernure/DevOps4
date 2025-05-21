pipeline {
    agent any

    environment {
        IMAGE_NAME = 'munevvernure/devops4:latest'
        DOCKER_CREDS = credentials('dockerhub-creds-id')
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/munevvernure/DevOps4.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
                sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin'
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy to Minikube') {
            steps {
                withEnv(["KUBECONFIG=/home/jenkins/.kube/config"]) {
                    sh '''
                        kubectl apply --validate=false -f k8s/deployment.yaml
                        kubectl apply --validate=false -f k8s/service.yaml
                    '''
                }
            }
        }

    }
}
