pipeline {
    agent {
        docker {
            image 'python:3.11-slim'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        DOCKER_CONFIG = "${env.WORKSPACE}/.docker"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/psu6510110340/jenkins-demo-app.git'
            }
        }

        stage('Prepare (Docker CLI + Python deps)') {
            steps {
                sh '''
                  apt-get update
                  apt-get install -y --no-install-recommends docker.io
                  rm -rf /var/lib/apt/lists/*

                  python -m pip install --upgrade pip
                  pip install --no-cache-dir -r requirements.txt
                '''
            }
        }

        stage('Build Image') {
            steps {
                sh 'mkdir -p $DOCKER_CONFIG'
                sh 'docker build -t jenkins-demo-app:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker rm -f demo-app || true'
                sh 'docker run -d -p 8082:8082 --name demo-app jenkins-demo-app:latest'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Running tests..."'
                sh 'pytest || true'
            }
        }
    }
}
