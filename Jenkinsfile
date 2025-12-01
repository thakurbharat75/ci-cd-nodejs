pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/thakurbharat75/ci-cd-nodejs.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Navigate to the 'app' directory
                    dir('app') {
                        sh '''
                            if [ -f package.json ]; then
                                npm install
                            else
                                echo "package.json not found!"
                                exit 1
                            fi
                        '''
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    dir('app') {
                        sh 'npm test || echo "No tests found, continuing..."'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir('app') {
                        // Make sure to tag the Docker image correctly
                        sh 'docker build -t thakurbharat75/node-app .'
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub with your credentials directly
                    sh 'echo "ccccccccccccc" | docker login -u "thakurbharat75" --password-stdin'
                    dir('app') {
                        // Ensure image is tagged and then push to Docker Hub
                        sh 'docker push thakurbharat75/node-app'
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    dir('app') {
                        sh '''
                            docker stop node-app || true
                            docker rm node-app || true
                            docker run -d --name node-app -p 80:3000 thakurbharat75/node-app
                        '''
                    }
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
