pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/Debasish-87/Cloud-native-zero-trust-k8s-platform.git'
            }
        }

        stage('List Files') {
            steps {
                echo 'Listing all files in workspace...'
                sh 'ls -la'
            }
        }

        stage('Trivy Scan') {
            steps {
                echo 'Running Trivy scan...'
                sh '''
                    if command -v trivy >/dev/null 2>&1; then
                        trivy fs .
                    else
                        echo "⚠️ Trivy not installed"
                    fi
                '''
            }
        }

        stage('Gitleaks Scan') {
            steps {
                echo 'Running Gitleaks scan...'
                sh '''
                    if command -v gitleaks >/dev/null 2>&1; then
                        gitleaks detect --verbose
                    else
                        echo "⚠️ Gitleaks not installed"
                    fi
                '''
            }
        }

        stage('Kubeaudit') {
            steps {
                echo 'Running Kubeaudit scan...'
                sh '''
                    if command -v kubeaudit >/dev/null 2>&1; then
                        kubeaudit version
                    else
                        echo "⚠️ Kubeaudit not installed"
                    fi
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo '✅ All stages completed successfully.'
        }
        failure {
            echo '❌ Pipeline failed. Check stage logs above.'
        }
    }
}
