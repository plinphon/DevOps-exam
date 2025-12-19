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
                lsof -i :4444 -t | xargs -r kill -9 || true

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
