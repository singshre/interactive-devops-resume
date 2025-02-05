pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    try {
        git branch: 'main', url: 'https://github.com/Singshre/interactive-devops-resume.git'
                    } catch (Exception e) {
                        error "Failed to clone repository: ${e.message}"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        dockerImage = docker.build("singshre2025/interactive-resume:${env.BUILD_NUMBER}")
                    } catch (Exception e) {
                        error "Failed to build Docker image: ${e.message}"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    try {
                        docker.withRegistry('', DOCKER_HUB_CREDENTIALS) {
                            dockerImage.push()
                            dockerImage.push('latest')
                        }
                    } catch (Exception e) {
                        error "Failed to push Docker image: ${e.message}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    try {
                        sh '''
                            kubectl set image deployment/interactive-resume interactive-resume=singshre2025/interactive-resume:${BUILD_NUMBER}
                        '''
                    } catch (Exception e) {
                        error "Failed to deploy to Kubernetes: ${e.message}"
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
