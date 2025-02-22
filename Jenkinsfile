pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/in28minutes/spring-boot-examples.git'
        GIT_BRANCH = 'main'
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = 'your-account-id'
        ECR_REPO = 'spring-boot-app-repo'   // Exact name for AWS ECR repo
        IMAGE_NAME = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
        DOCKER_HUB_REPO = 'your-dockerhub-username/springboot-app'  // Replace with your DockerHub username
    }

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build Application using Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login to Docker Hub & Push Image') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh 'docker tag $IMAGE_NAME $DOCKER_HUB_REPO:latest'
                    sh 'docker push $DOCKER_HUB_REPO:latest'
                }
            }
        }

        stage('Login to AWS ECR & Push Image') {
            steps {
                withAWS(credentials: 'aws-credentials-id', region: "${AWS_REGION}") {
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com'
                    sh 'docker tag $IMAGE_NAME $IMAGE_NAME'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy to AWS ECS') {
            steps {
                withAWS(credentials: 'aws-credentials-id', region: "${AWS_REGION}") {
                    sh '''
                    aws ecs update-service --cluster spring-boot-ecs-cluster --service spring-boot-service --force-new-deployment
                    '''
                }
            }
        }
    }
}
