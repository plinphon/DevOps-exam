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
                sh 'npm test'
            }
        }

        stage('Deploy to Target') {
            steps {
                sh '''
                pkill -f "node index.js" || true
                nohup node index.js > app.log 2>&1 &
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
