
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'devops-html-app'
        CONTAINER_NAME = 'devops-html-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Test HTML') {
            steps {
                sh '''
                    docker run --rm \
                      --entrypoint sh \
                      "$IMAGE_NAME:$BUILD_NUMBER" \
                      -c 'test -f /usr/share/nginx/html/index.html &&
                          grep -q "DevOps HTML App" /usr/share/nginx/html/index.html'
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f "$CONTAINER_NAME" || true

                    docker run -d \
                      --name "$CONTAINER_NAME" \
                      -p 8080:80 \
                      "$IMAGE_NAME:$BUILD_NUMBER"
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline successful! App deployed on port 8080.'
        }

        failure {
            echo 'Pipeline failed. Check the stage logs.'
        }
    }
}
