pipeline {
    agent { label 'dev' }

    stages {

        stage('Pre-Cleanup & Permission Fix') {
            steps {
                echo '🧹 Cleaning old containers and fixing MySQL volume permissions...'
                script {
                    // Stop and remove all old containers (ignore errors)
                    sh '''
                        docker stop $(docker ps -aq) || true
                        docker rm $(docker ps -aq) || true
                    '''

                    // Fix MySQL data folder permissions for MySQL container (UID 999)
                    sh '''
                        sudo chown -R 999:999 mysql-data || true
                        sudo chmod -R 777 mysql-data || true
                    '''
                }
            }
        }

        stage('Clone Repository') {
            steps {
                echo '🌀 Cleaning old code but keeping MySQL data safe...'
                script {
                    sh 'find . -mindepth 1 -maxdepth 1 ! -name "mysql-data" -exec rm -rf {} +'
                }

                git url: 'https://github.com/asimullah312/two-tier-flask-app.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                script {
                    sh 'docker build --no-cache -t two-tier-flask-app .'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                echo '🚀 Deploying application using Docker Compose...'
                script {
                    sh '''
                        # Stop old containers and remove volumes to ensure MySQL can initialize
                        docker compose down -v || true

                        # Start new containers
                        docker compose up -d
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful! Both MySQL and Flask are running.'
        }
        failure {
            echo '❌ Deployment Failed. Check logs for details.'
        }
    }
}
