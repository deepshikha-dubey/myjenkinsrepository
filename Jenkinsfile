
pipeline {
    agent any

    environment {
        REGISTRY = "your_ecr_registry_url"
        REPO = "your_repo_name"
        IMAGE = "${env.REPO}:${env.BUILD_NUMBER}"
        AWS_CREDENTIALS = "your_aws_credentials_id"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git 'https://your-repo-url.git'
            }
        }

        stage('Docker Build and Push') {
            steps {
                script {
                    docker.withRegistry("https://${env.REGISTRY}", "${env.AWS_CREDENTIALS}") {
                        def image = docker.build("${env.IMAGE}")
                        image.push()
                    }
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                script {
                    sh '''
                    aws ecs update-service --cluster your-cluster-name --service your-service-name --force-new-deployment
                    '''
                }
            }
        }
    }

    post {
        success {
            slackSend (channel: '#your-channel', color: 'good', message: "Deployment Successful: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}")
        }
        failure {
            slackSend (channel: '#your-channel', color: 'danger', message: "Deployment Failed: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}")
        }
    }
}
