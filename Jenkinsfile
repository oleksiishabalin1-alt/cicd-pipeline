pipeline {
    agent any
    
    tools {
        nodejs 'node' // Назва, яку ти дав у Global Tool Configuration
    }

    environment {
        // Визначаємо змінні залежно від гілки
        PORT = "${BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "${BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG = "v1.0"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker build') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Зупиняємо старий контейнер саме для цієї гілки
                    sh "docker stop ${IMAGE_NAME} || true"
                    sh "docker rm ${IMAGE_NAME} || true"
                    
                    // Запуск: внутрішній порт застосунку 3000, зовнішній — динамічний
                    sh "docker run -d --name ${IMAGE_NAME} -p ${PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}
