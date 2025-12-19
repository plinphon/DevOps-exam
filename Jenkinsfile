pipeline {
    agent any

    environment {
        IMAGE_NAME = 'plinphonpat/myapp2:latest'
        KUBE_DEPLOYMENT = 'deployment'
        KUBE_SERVICE = 'service'
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
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sshagent(['ssh-credentials-id']) {
                    sh 'ssh laborant@172.16.0.5 "kubectl apply -f ~/deployment.yaml"'
                }
            }
        }
        
    }

    post {
        success {
            echo '✅ Deployed successfully!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
