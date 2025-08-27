pipeline {
    agent {
        docker {
            image 'docker:20.10.7'
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
                sh 'docker exec demo-app pytest || true'
            }
        }
    }
}
