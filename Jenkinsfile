pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-ecommerce-backend"
        DOCKER_TAG = "${BUILD_NUMBER}"
        REGISTRY = "meghanavalluri/my-ecommerce"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Cloning the repository..."
                    git branch: 'main', url: 'https://github.com/meghanavalluri02/ecommerce-project.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker Image..."
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ./backend"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    echo "Logging in to Docker Hub..."
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) 
                    {
                        sh "docker login -u $DOCKER_USER -p $DOCKER_PASS"
                    }
                    echo "Pushing image to Docker Hub..."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}:${DOCKER_TAG}"
                    sh "docker push ${REGISTRY}:${DOCKER_TAG}"
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    echo "Stopping existing container if running..."
                    sh "docker stop ecommerce || true"
                    sh "docker rm ecommerce || true"
                    
                    echo "Deploying new container..."
                    sh "docker run -d -p 5000:5000 --name ecommerce ${REGISTRY}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
