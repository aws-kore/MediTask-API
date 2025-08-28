pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                git 'https://github.com/aws-kore/MediTask-API.git'
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    docker.build("meditask-api")
                }
            }
        }

        stage('Unit Tests') {
            steps {
                script {
                    sh 'docker run --rm meditask-api pytest tests/unit || true'
                }
            }
        }

        stage('Integration Tests') {
            steps {
                script {
                    sh 'docker run --rm meditask-api pytest tests/integration || true'
                }
            }
        }

        stage('Vulnerability Scan') {
            steps {
                script {
                    // Safety checks for dependencies
                    sh 'docker run --rm meditask-api safety check || true'
                }
            }
        }

        stage('Security Analysis') {
            steps {
                script {
                    // Bandit for static code analysis
                    sh 'docker run --rm meditask-api bandit -r . || true'
                }
            }
        }

        stage('Run container') {
            steps {
                script {
                    sh 'docker rm -f meditask-container || true'
                    sh 'docker run -d -p 8000:8000 --name meditask-container meditask-api'
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    // Simple API health check
                    sh 'sleep 5 && curl -f http://localhost:8000/ || exit 1'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}

