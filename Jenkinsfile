pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'        
        ECR_REPOSITORY = 'dev/assingement' 
        IMAGE_TAG = "$version2"    
        AWS_ACCOUNT_ID = '314146334258' // Replace with your AWS account ID
        DOCKER_IMAGE = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/sudheer76R/java-example.git'
            }
        }

        stage('Build Stage') {
            steps {  
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Push to ECR') {
            steps {          
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                    sh "docker push ${DOCKER_IMAGE}"
            }
        }

        stage('Depoly Stage') {
            steps {
                    sh """
                        ssh -o StrictHostKeyChecking=no user@docker-server << 'EOF'
                            docker pull ${DOCKER_IMAGE}
                            docker stop my_container || true
                            docker rm my_container || true
                            docker run -d --name my_container ${DOCKER_IMAGE}
                        EOF
                    """
            }
        }
    }

    post {
        always {
            // Clean up actions, for example, removing temporary Docker images
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
