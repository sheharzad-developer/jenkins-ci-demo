pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Source code downloaded from GitHub'
            }
        }

        stage('Show Workspace') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('jenkins-ci-demo') {
                    sh 'pwd'
                    sh 'ls -la'
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('jenkins-ci-demo') {
                    sh 'npm test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('jenkins-ci-demo') {
                    sh 'docker build -t jenkins-ci-demo:v1 .'
                }
            }
        }
    }
}