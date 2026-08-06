pipeline {
    agent any

    environment {
        IMAGE_NAME = 'jenkins-ci-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('jenkins-ci-demo') {
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                dir('jenkins-ci-demo') {
                    sh 'npm test'
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('jenkins-ci-demo') {
                    sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
                }
            }
        }
    }
}
