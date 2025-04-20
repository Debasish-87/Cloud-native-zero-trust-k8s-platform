pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        TRIVY_IMAGE = "aquasec/trivy"
        GITLEAKS_IMAGE = "zricethezav/gitleaks"
        KUBEAUDIT_IMAGE = "shopify/kubeaudit"
    }

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
                        deleteDir()
                        git branch: 'main', url: 'https://github.com/Debasish-87/ZeroTrustOps-Platform.git'
                        sh 'ls -la'
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                echo '🐳 Running Trivy Image Scan...'
                sh '''
                    mkdir -p reports/trivy
                    docker run --rm -v $(pwd):/root ${TRIVY_IMAGE} image --format json -o /root/reports/trivy/image-scan.json nginx:alpine
                '''
            }
        }

        stage('Trivy Config Scan') {
            steps {
                echo '🔍 Checking Trivy version...'
                sh 'docker run --rm aquasec/trivy --version'
                echo '📄 Running Trivy Config (YAML/IaC) Scan...'
                sh '''
                    mkdir -p reports/trivy
                    pwd
                    docker run --rm -v $(pwd):/root aquasec/trivy image --format json -o /root/reports/trivy/image-scan.json nginx:alpine
                '''

            }
        }

        stage('Gitleaks Secrets Scan') {
            steps {
                echo '🔐 Checking Gitleaks version...'
                sh 'docker run --rm zricethezav/gitleaks version'
                echo '🔐 Running Gitleaks Scan...'
                sh '''
                mkdir -p reports/trivy
                docker run --rm \
                    -v $(pwd)/reports/trivy:/report \
                    aquasec/trivy image --format json -o /report/image-scan.json nginx:alpine
                '''
            }
        }

        stage('Kubeaudit Misconfig Scan') {
            steps {
                echo '🛡️  Checking Kubeaudit version...'
                sh 'docker run --rm shopify/kubeaudit version'
                echo '🛡️  Running Kubeaudit Scan...'
                sh '''
                    mkdir -p reports/kubeaudit
                    docker run --rm -v $(pwd)/manifests/dev:/manifests ${KUBEAUDIT_IMAGE} all -f /manifests/deployment.yaml -f /manifests/service.yaml --format json > reports/kubeaudit/kubeaudit-report.json
                '''
            }
        }
    }

    post {
        always {
            echo '✅ All scans complete. Reports saved in reports/ directory.'
        }
    }
}








// kubectl port-forward svc/argocd-server -n argocd 8080:443  -- argoCD running

// kubectl port-forward svc/demo-app 9090:80 -n dev  -- pod running

