pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t django-todo .'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop django-todo || true
                docker rm django-todo || true
                docker run -d -p 8000:8000 --name django-todo django-todo
                '''
            }
        }
    }
}
