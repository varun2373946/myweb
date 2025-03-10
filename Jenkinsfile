pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        PROJECT_NAME = "my-java-app"
        SONARQUBE_URL = "http://98.84.151.19:9000"
        AWS_ECR_REPO = "476114133216.dkr.ecr.us-east-1.amazonaws.com/mywebrepo"
        DOCKER_IMAGE_NAME = "myweb-app"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "Cloning the repository..."
                    sh '''
                        rm -rf myweb || true
                        git clone https://github.com/varun2373946/myweb.git
                        cd myweb && git checkout main
                    '''
                }
            }
        }

        stage('SonarQube Scan') {
            steps {
                script {
                    echo "Running SonarQube analysis..."
                    sh '''
                        cd myweb
                        ./mvnw sonar:sonar -Dsonar.host.url=${SONARQUBE_URL} \
                                           -Dsonar.login=${SONARQUBE_TOKEN}
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh '''
                        cd myweb
                        docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} .
                    '''
                }
            }
        }

        stage('Push to AWS ECR') {
            steps {
                script {
                    echo "Logging into AWS ECR..."
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ECR_REPO}"

                    echo "Tagging Docker image..."
                    sh "docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} ${AWS_ECR_REPO}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"

                    echo "Pushing Docker image to ECR..."
                    sh "docker push ${AWS_ECR_REPO}/${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! 🚀"
        }
        failure {
            echo "Pipeline failed! Check the logs. ❌"
        }
    }
}
``
