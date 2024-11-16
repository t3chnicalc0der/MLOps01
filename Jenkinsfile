pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIAL_ID = 'mlops-jenkins-dockerhub-token'
        DOCKERHUB_REGISTRY = 'https://registry.hub.docker.com'
        DOCKERHUB_REPOSITORY = 'iquantc/mlops-proj-01'
    }
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
                    sh "python -m pip install --break-system-packages -r requirements.txt"
                    sh "pylint app.py train.py --output=pylint-report.txt --exit-zero"
                    sh "flake8 app.py train.py --ignore=E501,E302 --output-file=flake8-report.txt"
                    sh "black app.py train.py"
                }
            }
        }
        stage('Test Code') {
            steps {
                // Pytest code
                script {
                    echo 'Testing Python Code...'
                    sh "pytest tests/"
                }
            }
        }
        stage('Trivy FS Scan') {
            steps {
                // Trivy Filesystem Scan
                script {
                    echo 'Scannning Filesystem with Trivy...'
                    sh "trivy fs ./ --format table -o trivy-fs-report.html"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker Image
                script {
                    echo 'Building Docker Image...'
                    dockerImage = docker.build("${DOCKERHUB_REPOSITORY}:latest") 
                }
            }
        }
        stage('Trivy Docker Image Scan') {
            steps {
                // Trivy Docker Image Scan
                script {
                    echo 'Scanning Docker Image with Trivy...'
                    sh "trivy image ${DOCKERHUB_REPOSITORY}:latest --format table -o trivy-image-report.html"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                // Push Docker Image to DockerHub
                script {
                    echo 'Pushing Docker Image to DockerHub...'
                    docker.withRegistry("${DOCKERHUB_REGISTRY}", "${DOCKERHUB_CREDENTIAL_ID}"){
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('kubectl'){
            steps {
                sh '''
                    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
                    chmod +x kubectl
                    mv kubectl /usr/local/bin/
                '''
            }
        }
        stage('Deploy') {
            steps {
                // Deploy Image to Amazon EKS
                script {
                    echo 'Deploying to production...'
                    kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJUFBqeW05bXgwN2t3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TkRFd01qUXdOVEU1TVRKYUZ3MHpOREV3TWpJd05USTBNVEphTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUUMvWUlLbEtNSStFcitwZTAxS241UkgxWVdkUEh0WlN3dCswZnVKUXQ5YWErNStqcjd6MTJCZjR5dlUKYVZxUERZR2tWUE9PbitlUlpENjhJNmhWL0QyMUhYMnc5R1dxbmFJOWcrQjBrRTFESmhWdlpiSDBqMVd0ano5VQpvS1piNjFzbUN0elYyUE9qcmJKZUxOMlRaRU5VY2V6Uzc4aDFRRHNVTTh4ek9FSnlkRGR2NFVpT0tsbVFuSWF4CmlmRnY1QlRabENadkxVRXJGZXlvRUgxZnFuUXNad0U2MllxVUkxTWwway8wdHN0ajkzbU1lTmJNMXJWZUJsbzIKNk9SUHZzNFFZb0tXZUt4Q3N1YzVsbm1VSEl4OUR3OU9tZmN4UzkvN2lzUU5OMUpaZ2xhTU1OSlBOK2tzTlIrRQphWWU4S2xoVmM3V205SWQybERweXdJL2lTUWhaQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJRNWdydVlkRGlKYkNpazYvQWcvTko5dHc2MmpEQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUhzL216eGYwLwptdHhHRlNOZzhrRGRYTFZYNWFwMWN0TmdueFdQOXh3aklydVZrcU5pa0t5NlZSNlFuL0t0SVBPenZOWDgvc3hsCnlpdm5vVFZHTkVBdkx6c056bWpwYXR4RnFkNlVFUW1Vd2VNMmp6LzgxZFVabHZweEpVbGZOUWFkTVBQeVFrODkKazNQVndoV05aVWtBV2gzQ1FjUXRXalFrQ0RNbHg5RGVHUGp3NGN5MUZUNkR0b29qcGtwQ1VJSlBac2J5amRQQQo2ZDRjc0ZMUU5oYncxUUt5SHdwQ0p5K1Ird1ZYOHdKQVlkcER0bnltcmRRNUtkQlU2OHRlUlREOXdRTGJHamQyCjJkTlBqUkk1amNsWXR1WkFIb1hDdjNGbzBHWi9YV21yeUVpNEVtQzZKS0tTUU5ldmVNUEtuYVFuT1ZQZ0ovUkMKTDZoMlpiZEZ2QWlsCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K', credentialsId: 'kubeconfig', serverUrl: 'https://6E672001ADE08CB2DB34D6827CFEDF66.gr7.us-east-1.eks.amazonaws.com') {
                        sh "kubectl apply -f kubernetes/deployment.yaml"
                        sh "kubectl apply -f kubernetes/service.yaml"
                    }
                }
            }
        }
    }
}
