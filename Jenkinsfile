
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/AyushB-coder/healthcare-devsecops.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t healthcare-app:${BUILD_NUMBER} .'
            }
        }

        stage('Trivy Security Scan') {
            steps {
                sh '''
                    trivy image \
                    --severity HIGH,CRITICAL \
                    --exit-code 1 \
                    healthcare-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    docker rm -f healthcare-secure-container || true
                    docker run -d \
                    --name healthcare-secure-container \
                    -p 8082:80 \
                    healthcare-app:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo 'Healthcare application scanned and deployed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the build or security scan results.'
        }
    }
}
