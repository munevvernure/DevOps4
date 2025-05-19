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
                sh './mvnw clean package -DskipTests'
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
                withCredentials([string(credentialsId: 'kubeconfig-content', variable: 'KUBECONFIG_RAW')]) {
                    sh '''
                        echo "$KUBECONFIG_RAW" > kubeconfig.yaml
                        export KUBECONFIG=$(pwd)/kubeconfig.yaml
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    '''
                }
            }
        }
    }
}
