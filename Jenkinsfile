pipeline {
    agent { 
        docker { 
            image 'python:3.9-slim'  // runs the pipeline inside this container
            args '-v /var/run/docker.sock:/var/run/docker.sock' 
        }
    }
    environment {
        IMAGE_NAME = "user-service"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'YOUR_GITHUB_REPO_URL'
            }
        }
        stage('Install & Test') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pytest --maxfail=1 --disable-warnings -v
                '''
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }
        stage('Docker Run') {
            steps {
                sh '''
                docker stop $IMAGE_NAME || true
                docker rm $IMAGE_NAME || true
                docker run -d -p 5000:5000 --name $IMAGE_NAME $IMAGE_NAME
                '''
            }
        }
    }
}
