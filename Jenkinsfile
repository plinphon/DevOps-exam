pipeline {
    agent any

    environment {
        IMAGE_NAME = 'your-dockerhub-username/myapp:latest'
        KUBE_DEPLOYMENT = 'myapp-deployment'
        KUBE_SERVICE = 'myapp-service'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Node') {
            steps {
                sh 'node -v'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'export NODE_ENV=test && npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t $IMAGE_NAME .
                    docker push $IMAGE_NAME
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f pod.yaml
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployed to TARGET'
        }
        failure {
            echo '❌ failed'
        }
    }
}
