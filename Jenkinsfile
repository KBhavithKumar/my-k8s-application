pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/SanjayAmaravadi/my-node-application.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t my-node-application:${BUILD_NUMBER} .
                docker tag my-node-application:${BUILD_NUMBER} SanjayAmaravadi/my-node-application:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push SanjayAmaravadi/my-node-application:latest'
            }
        }

        stage('Start Minikube if not running') {
            steps {
                sh '''
                if ! minikube status | grep -q "apiserver: Running"; then
                    echo "Minikube is not running. Starting now..."
                    minikube start --driver=docker --memory=2048 --cpus=2
                fi
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                # Load latest image into Minikube
                minikube image load SanjayAmaravadi/my-node-application:latest

                # Apply manifests
                minikube kubectl -- apply -f k8s/deployment.yaml
                minikube kubectl -- apply -f k8s/service.yaml
                minikube service my-node-application
                '''
            }
        }
    }
}
