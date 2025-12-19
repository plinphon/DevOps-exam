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
                # Kill any process using port 4444 (with multiple attempts)
                lsof -i :4444 -t | xargs -r kill -9 || true
                sleep 2  # Wait for port to be released
                
                # Double check and force kill if still exists
                if lsof -i :4444 > /dev/null 2>&1; then
                    echo "Port 4444 still in use, forcing kill..."
                    lsof -i :4444 -t | xargs -r kill -9 || true
                    sleep 2
                fi

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
