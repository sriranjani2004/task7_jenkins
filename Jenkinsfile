pipeline {
    agent any

    tools {
        dockerTool 'Docker'
    }

    environment {
        PYTHON_HOME = "/usr/local/bin/python"
        DOCKER_HOME = "/usr/local/bin/docker"
        PATH = "${PYTHON_HOME}:${DOCKER_HOME}:/usr/bin:/bin:/usr/sbin:/sbin"
        DOCKER_IMAGE = "flask-app"
    }

    stages {
        stage('Initialize') {
            steps {
                script {
                    echo "Initializing Pipeline..."
                    sh 'python --version'
                    sh 'docker --version'
                }
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/sriranjani2004/task7_jenkins'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo "Installing Python dependencies..."
                    sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('Build Docker Image') {
            when { expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' } }
            steps {
                script {
                    echo "Building Docker Image..."
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Deploy with Docker') {
            when { expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' } }
            steps {
                script {
                    echo "Deploying application using Docker..."
                    sh 'docker stop flask-container || echo Container not running'
                    sh 'docker rm flask-container || echo Container not found'
                    sh 'docker run -d -p 5000:5000 --name flask-container $DOCKER_IMAGE'
                    echo "Application is accessible at http://localhost:5000"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful! Flask App is running on port 5000'
        }
        failure {
            echo 'Deployment Failed. Check logs!'
        }
    }
}
