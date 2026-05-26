pipeline {
    agent any

    environment {
        DOCKERHUB = "tuongvan23521768"
    }

    stages {

        stage('Clone Source') {
            steps {
                git 'https://github.com/van2352/Nhom04-NT548-microservices.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=nhom04-nt548 \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t $DOCKERHUB/user-service:latest ./service-user
                docker build -t $DOCKERHUB/product-service:latest ./service-product
                '''
            }
        }

        stage('Push Docker Images') {
            steps {
                sh '''
                docker push $DOCKERHUB/user-service:latest
                docker push $DOCKERHUB/product-service:latest
                '''
            }
        }

        stage('Deploy Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/
                kubectl rollout restart deployment/user-service
                kubectl rollout restart deployment/product-service
                '''
            }
        }
    }
}
