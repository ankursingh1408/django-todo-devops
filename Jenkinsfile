pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t django-todo .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker stop django-todo || true
                docker rm django-todo || true
                docker run -d -p 8000:8000 --name django-todo django-todo
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 5
                curl -f http://localhost:8000/loginn/ || exit 1
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed! Check logs for details.'
        }
    }
}
