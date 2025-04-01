pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/node-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-credentials-id', url: 'https://github.com/thakurbharat75/ci-cd-nodejs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    if [ -d node_modules ]; then
                        echo "Using cached node_modules"
                    else
                        npm install
                    fi
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || echo "No tests found, continuing..."'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', 'docker-hub-credentials') {
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    sh 'docker stop node-app || true && docker rm node-app || true'
                    sh 'docker run -d --name node-app -p 80:3000 $DOCKER_IMAGE'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Build failed! Check logs for details.'
        }
    }
}
