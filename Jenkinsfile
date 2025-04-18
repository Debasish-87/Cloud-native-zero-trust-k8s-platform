pipeline {
    agent any

    options {
        skipDefaultCheckout(true) // We manually handle Git
    }

    // triggers {
    //     pollSCM('H/5 * * * *') // Optional: Check every 5 mins for Git changes
    // }

    stages {
        stage('Clone Repo If Changed') {
            steps {
                script {
                    def repoChanged = false
                    def gitDir = '.git'

                    if (fileExists(gitDir)) {
                        echo '🔄 Checking for remote changes...'
                        def oldRev = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                        sh 'git remote update'
                        def newRev = sh(script: 'git rev-parse origin/main', returnStdout: true).trim()
                        if (oldRev != newRev) {
                            echo "🆕 Changes detected in repo. Pulling latest..."
                            repoChanged = true
                        } else {
                            echo "✅ No changes in repo. Skipping clone."
                        }
                    } else {
                        echo "📥 No local repo found. Cloning fresh..."
                        repoChanged = true
                    }

                    if (repoChanged) {
                        // Delete old contents before fresh checkout
                        deleteDir()
                        git branch: 'main', url: 'https://github.com/Debasish-87/Cloud-native-zero-trust-k8s-platform.git'
                        sh 'ls -la'
                    }
                }
            }
        }

        stage('Check Trivy Version') {
            steps {
                echo '🔍 Checking Trivy version...'
                sh 'docker run --rm aquasec/trivy --version'
            }
        }

        stage('Check Gitleaks Version') {
            steps {
                echo '🔐 Checking Gitleaks version...'
                sh 'docker run --rm zricethezav/gitleaks version'
            }
        }

        stage('Check Kubeaudit Version') {
            steps {
                echo '🛡️  Checking Kubeaudit version...'
                sh 'docker run --rm shopify/kubeaudit version'
            }
        }
    }

    post {
        always {
            echo '✅ Pipeline complete.'
        }
    }
}


