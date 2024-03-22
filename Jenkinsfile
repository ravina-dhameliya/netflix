pipeline {
    agent any

    environment {
        TMDB_V3_API_KEY = '957c8ceced7b2c2f870ee339d7e0ad43'
        ECR_REPO = '465764343565.dkr.ecr.eu-west-2.amazonaws.com/netflix'
        K8S_DEPLOYMENT_MANIFEST = 'Kubernetes/deployment.yml'
        K8S_SERVICE_MANIFEST = 'Kubernetes/service.yml'
        AWS_ACCESS_KEY_ID = 'AKIAWY4NX34G57RP77J3'
        AWS_SECRET_ACCESS_KEY = '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'
        EKS_CLUSTER_NAME = 'darshan-kachhia'
        AWS_DEFAULT_REGION = 'eu-west-2'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://darshank188: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@github.com/darshank188/netflix.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build --build-arg TMDB_V3_API_KEY=${TMDB_V3_API_KEY} -t netflix ."
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region eu-west-2 | docker login --username AWS --password-stdin 465764343565.dkr.ecr.eu-west-2.amazonaws.com"
                    sh "docker tag netflix:latest 465764343565.dkr.ecr.eu-west-2.amazonaws.com/netflix:latest"
                    sh "docker push 465764343565.dkr.ecr.eu-west-2.amazonaws.com/netflix:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "aws eks --region ${AWS_DEFAULT_REGION} update-kubeconfig --name ${EKS_CLUSTER_NAME}"
                    sh "kubectl apply -f ${K8S_DEPLOYMENT_MANIFEST}"
                    sh "kubectl apply -f ${K8S_SERVICE_MANIFEST}"
                }
            }
        }
    }
}
