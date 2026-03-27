pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/KBhavithKumar/my-k8s-application.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
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

     stage('Building Docker Image and Deploy to Kubernetes') {
    steps {
        sh '''
        eval $(minikube -p minikube docker-env)
        # Build image directly inside Minikube
        minikube image build -t my-k8s-application:${BUILD_NUMBER} .

        # Tag latest
        minikube image tag my-k8s-application:${BUILD_NUMBER} my-k8s-application:latest

        # Apply manifests
        minikube kubectl -- apply -f k8s/deployment.yaml
        minikube kubectl -- apply -f k8s/service.yaml

        # Access service
        minikube service my-k8s-application
        '''
    }
}
    }
}
