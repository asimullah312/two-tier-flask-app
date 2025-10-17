pipeline {
    agent { label 'dev' }  // Run this pipeline on agent with label 'docker-agent'

    stages {

        stage('Clone Repository') {
            steps {
                echo '🌀 Cleaning old code but keeping MySQL data safe...'
                script {
                    // ✅ Delete all old files except MySQL data folder
                    sh 'find . -mindepth 1 -maxdepth 1 ! -name "mysql-data" -exec rm -rf {} +'
                }

                // ✅ Pull latest code from GitHub
                git url: 'https://github.com/asimullah312/two-tier-flask-app.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image (fresh build, no cache)...'
                script {
                    // ✅ Force rebuild to include new code changes
                    sh 'docker build --no-cache -t two-tier-flask-app .'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo '🚀 Deploying application using Docker Compose...'
                script {
                    // ✅ Stop old containers if running
                    sh 'docker compose down || true'

                    // ✅ Start new containers with latest image
                    sh 'docker compose up -d'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed. Please check logs.'
        }
    }
}
