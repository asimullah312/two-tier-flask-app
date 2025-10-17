pipeline {
    agent { label 'dev' }  // 👈 Run this pipeline on agent with label 'docker-agent'

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Cloning code from GitHub...'
                git url: 'https://github.com/asimullah312/two-tier-flask-app.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    sh 'docker build -t two-tier-flask-app .'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo 'Deploying application using Docker Compose...'
                script {
                    // Stop old containers (if running)
                    sh 'docker-compose down || true'

                    // Start new containers in detached mode
                    sh 'docker-compose up -d'
                }
            }
        }
    }
}
