pipeline {
    agent any

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

        stage('Unit Tests') {
            steps {
                sh '''
                # Stop anything using port 4444
                fuser -k 4444/tcp || true

                # Run tests
                npm test
                '''
            }
        }

        stage('Deploy Docker') {
            steps {
                sh '''
                docker build -t myapp:latest .
                            docker stop myapp || true
                            docker rm myapp || true
                            docker run -d \
                                --name myapp \
                                -p 4444:4444 \
                                --restart unless-stopped \
                                myapp:latest
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
