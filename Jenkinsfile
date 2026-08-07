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
                        echo "Username: $DOCKER_USER"
                        echo "Password length: $(echo -n "$DOCKER_PASS" | wc -c)"

                        docker logout || true

                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                        docker push sheharzad/jenkins-ci-demo:${BUILD_NUMBER}
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                dir('jenkins-ci-demo') {
                    sh '''
                        # Docker Desktop's Kubernetes API server is only reachable from
                        # the host at 127.0.0.1:<dynamic-port>; from inside this container
                        # that must be host.docker.internal instead, and its cert isn't
                        # issued for that hostname, hence --insecure-skip-tls-verify.
                        mkdir -p ~/.kube
                        cp /host-kube/config ~/.kube/config

                        CLUSTER=$(kubectl config view --minify -o jsonpath='{.clusters[0].name}')
                        SERVER=$(kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}' | sed 's#127.0.0.1#host.docker.internal#')
                        kubectl config set-cluster "$CLUSTER" --server="$SERVER" --insecure-skip-tls-verify=true

                        helm upgrade --install jenkins-ci-demo ./helm/jenkins-ci-demo \
                            --set image.repository=sheharzad/jenkins-ci-demo \
                            --set image.tag=${BUILD_NUMBER}

                        kubectl rollout status deployment/jenkins-ci-demo
                    '''
                }
            }
        }
    }
}