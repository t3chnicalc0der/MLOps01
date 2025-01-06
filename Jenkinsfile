pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo 'Cloning GitHub Repository...'
                    checkout scmGit(
                        branches: [[name: '*/main']],
                        extensions: [],
                        userRemoteConfigs: [[
                            credentialsId: 'mlops-git-token',
                            url: 'https://github.com/t3chnicalc0der/MLOps01.git'
                        ]]
                    )
                }
            }
        }
        stage('Lint Code') {
            steps {
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
                script {
                    echo 'Testing Python Code...'
                }
            }
        }
        stage('Trivy FS Scan') {
            steps {
                script {
                    echo 'Scanning Filesystem with Trivy...'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker Image...'
                }
            }
        }
        stage('Trivy Docker Image Scan') {
            steps {
                script {
                    echo 'Scanning Docker Image with Trivy...'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker Image to DockerHub...'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying to production...'
                }
            }
        }
    }
}

