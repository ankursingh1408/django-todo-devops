pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ankursingh1408/django-todo-devops.git'
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
    }
}
