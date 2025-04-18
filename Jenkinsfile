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
