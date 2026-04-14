pipeline {
    agent any

    environment {
        IMAGE_NAME = 'busapooja/my-node-app'
        TAG = 'latest'
        CONTAINER_NAME = 'my-node-container'
        KUBECONFIG = '/home/jenkins/.kube/config'
    }

    stages {

        stage('Build') {
            steps {
                echo 'Building project...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push $IMAGE_NAME:$TAG'
            }
        }

        stage('Remove Old Container') {
            steps {
                sh '''
                    docker rm -f $CONTAINER_NAME || true
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker run -d -p 9096:80 --name $CONTAINER_NAME $IMAGE_NAME:$TAG
                '''
            }
        }
    }
}
