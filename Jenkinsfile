pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                // Clone Repository
                script {
                    echo 'Cloning GitHub Repository...'
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'mlops-git-token', url: 'https://github.com/iQuantC/MLOps01.git']])
                }
            }
        }
        stage('Lint Code') {
            steps {
                // Lint code
                script {
                    echo 'Linting Python Code...'
                //    sh "python -m pip install --break-system-packages -r requirements.txt"
                //    sh "pylint app.py train.py --output=pylint-report.txt --exit-zero"
                //    sh "flake8 app.py train.py --ignore=E501,E302 --output-file=flake8-report.txt"
                //    sh "black app.py train.py"
                }
            }
        }
        stage('Test Code') {
            steps {
                // Pytest code
                script {
                    echo 'Testing Python Code...'
                //    sh "pytest tests/"
                }
            }
        }
        stage('Trivy FS Scan') {
            steps {
                // Trivy Filesystem Scan
                script {
                    echo 'Scannning Filesystem with Trivy...'
                //    sh "trivy fs --format table -o trivy-fs-report.html"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker Image
                script {
                    echo 'Building Docker Image...'
                    //docker.build("mlops-app-01")
                }
            }
        }
        stage('Trivy Docker Image Scan') {
            steps {
                // Trivy Docker Image Scan
                script {
                    echo 'Scanning Docker Image with Trivy...'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                // Push Docker Image to DockerHub
                script {
                    echo 'Pushing Docker Image to DockerHub...'
                }
            }
        }
        stage('Deploy') {
            steps {
                // Deploy Image to Amazon EKS
                script {
                    echo 'Deploying to production...'
                }
            }
        }
    }
}
