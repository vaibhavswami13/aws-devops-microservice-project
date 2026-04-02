pipeline {
    agent any

    environment {
        IMAGE_NAME = "user-service"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vaibhavswami13/aws-devops-microservice-project'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker image..."
                docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Run Tests Inside Container') {
            steps {
                sh '''
                echo "Running tests inside Docker container..."
                docker run --rm $IMAGE_NAME pytest --maxfail=1 --disable-warnings -v || true
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                echo "Stopping old container (if exists)..."
                docker stop $IMAGE_NAME || true
                docker rm $IMAGE_NAME || true

                echo "Running new container..."
                docker run -d -p 5000:5000 --name $IMAGE_NAME $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "✅ App deployed successfully! Access it at http://<EC2-IP>:5000"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}
