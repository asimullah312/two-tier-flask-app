pipeline {
    agent { label 'dev' }  // Run this pipeline on agent with label 'docker-agent'

    stages {

        stage('Clone Repository') {
            steps {
                echo '🌀 Cleaning old workspace and cloning latest code...'
                // ✅ Remove old files to ensure no cached HTML or code
                deleteDir()

                // ✅ Always pull the latest code from GitHub
                git url: 'https://github.com/asimullah312/two-tier-flask-app.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building fresh Docker image...'
                script {
                    // ✅ Force rebuild without cache so new HTML changes apply
                    sh 'docker build --no-cache -t two-tier-flask-app .'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo '🚀 Deploying application using Docker Compose...'
                script {
                    // ✅ Stop old containers (if running)
                    sh 'docker compose down || true'

                    // ✅ Start new containers in detached mode
                    sh 'docker compose up -d'
                }
            }
        }
    }
}
