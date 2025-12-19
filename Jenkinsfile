pipeline {
    agent any

    environment {
        APP_PORT = "4444"
        APP_NAME = "node-target-app"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Node.js 24') {
            steps {
                sh '''
                if ! node -v | grep -q v24; then
                    sudo curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
                    sudo apt-get install -y nodejs
                fi
                node -v
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install express'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Deploy to Target') {
            steps {
                sh '''
                echo "Stopping"
                pkill -f "node index.js" || true

                echo "Starting application on target VM"
                node index.js
                '''
            }
        }
    }

    post {
        success {
            echo '✅ successfully'
        }
        failure {
            echo '❌ failed'
        }
    }
}
