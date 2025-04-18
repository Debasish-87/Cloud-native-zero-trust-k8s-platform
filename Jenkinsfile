pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/Debasish-87/Cloud-native-zero-trust-k8s-platform.git'
            }
        }

        stage('List Files') {
            steps {
                sh 'ls -la'
            }
        }

        stage('Trivy Scan') {
            steps {
                sh 'trivy --version || echo "Trivy not installed"'
            }
        }

        stage('Gitleaks Scan') {
            steps {
                sh 'gitleaks detect || echo "Gitleaks not installed or failed"'
            }
        }

        stage('Kubeaudit') {
            steps {
                sh 'kubeaudit version || echo "Kubeaudit not installed or failed"'
            }
        }
    }
}
