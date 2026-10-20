
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/riya28282021/cloud-devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t cloud-devops-demo .'
            }
        }

        stage('Test Website') {
            steps {
                sh 'docker run -d --name cloud-devops-test -p 8082:80 cloud-devops-demo'
                sh 'sleep 3'
                sh 'curl -f http://localhost:8082'
                sh 'docker rm -f cloud-devops-test'
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
