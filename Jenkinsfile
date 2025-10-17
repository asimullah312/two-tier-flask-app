pipeline {
    agent { label 'dev' }  // Run this pipeline on agent with label 'docker-agent'

    stages {

        stage('Pre-Cleanup & Permission Fix') {
            steps {
                echo '🧹 Fixing permissions and cleaning old containers...'
                script {
                    // Stop and remove all old containers (ignore errors)
                    sh '''
                    docker stop $(docker ps -aq) || true
                    docker rm $(docker ps -aq) || true
                    '''

                    // Fix workspace ownership and permissions automatically
                    sh '''
                    sudo chown -R $(whoami):$(whoami) $WORKSPACE || true
                    sudo chmod -R 777 $WORKSPACE || true
                    '''
                }
            }
        }

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
                    sh 'docker-compose down || true'

                    // ✅ Start new containers with latest image
                    sh '''
                    export UID=$(id -u)
                    export GID=$(id -g)
                    docker-compose up -d
                    '''
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
