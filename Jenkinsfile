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
                    sh 'docker build -t sheharzad/jenkins-ci-demo:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push sheharzad/jenkins-ci-demo:${BUILD_NUMBER}
                        docker logout
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                dir('jenkins-ci-demo') {
                    sh '''
                        kubectl apply -f k8s/
                        kubectl rollout status deployment/jenkins-ci-demo
                    '''
                }
            }
        }
    }
}