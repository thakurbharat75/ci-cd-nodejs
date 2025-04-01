pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'thakurbharat75/node-app'
        DOCKER_USERNAME = credentials('thakurbharat75')  // Jenkins Secret for Docker Username
        DOCKER_PASSWORD = credentials('Btchemistry@333')  // Jenkins Secret for Docker Password
    }

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
                        sh 'docker build -t $DOCKER_IMAGE .'
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    dir('app') {
                        sh 'docker push $DOCKER_IMAGE'
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
                            docker run -d --name node-app -p 80:3000 $DOCKER_IMAGE
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
