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

        stage('Install & Test') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop $IMAGE_NAME || true
                docker rm $IMAGE_NAME || true
                docker run -d -p 5000:5000 --name $IMAGE_NAME $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "Docker container is running on port 5000!"
        }
        failure {
            echo "Pipeline failed. Check the logs."
        }
    }
}
