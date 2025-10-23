pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-docker-example"
        CONTAINER_NAME = "jenkins-docker-container-${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Checking out source code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Container') {
            steps {
                echo " removing old container if exists.."
                sh 'docker rm -f $CONTAINER_NAME || true'
                echo "🚀 Running new container..."
                sh 'docker run -d --name $CONTAINER_NAME -p 5000:3000 $IMAGE_NAME'
            }
        }

        stage('Verify Container') {
            steps {
                echo " waiting for container to start..."
                sh 'sleep 5'
                echo "🔍 Verifying container is running..."
                sh 'docker ps | grep $CONTAINER_NAME'
                sh 'curl -s http://localhost:3000'
            }
        }

        stage('Cleanup') {
            steps {
                echo "🧹 Cleaning up containers..."
                sh '''
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                '''
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline finished — cleanup complete."
            sh ''' docker stop $CONTAINER_NAME || true docker rm $CONTAINER_NAME || true '''
        }
    }
}
