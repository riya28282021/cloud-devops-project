pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-2'
        ECR_REPO = '609865800610.dkr.ecr.ap-southeast-2.amazonaws.com/cloud-devops-demo'
        IMAGE_NAME = 'cloud-devops-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/riya28282021/cloud-devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
                sh 'docker tag $IMAGE_NAME:$BUILD_NUMBER $ECR_REPO:$BUILD_NUMBER'
                sh 'docker tag $IMAGE_NAME:$BUILD_NUMBER $ECR_REPO:latest'
            }
        }

        stage('Test Website') {
            steps {
                sh 'docker rm -f cloud-devops-test || true'
                sh 'docker run -d --name cloud-devops-test -p 8082:80 $IMAGE_NAME:$BUILD_NUMBER'
                sh 'sleep 3'
                sh 'curl --fail http://localhost:8082'
            }
        }

        stage('Push to Amazon ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region "$AWS_REGION" |
                    docker login --username AWS --password-stdin 609865800610.dkr.ecr.ap-southeast-2.amazonaws.com
                    docker push "$ECR_REPO:$BUILD_NUMBER"
                    docker push "$ECR_REPO:latest"
                '''
            }
        }
    }

    post {
        always {
            sh 'docker rm -f cloud-devops-test || true'
        }
        success {
            echo 'CI/CD pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
