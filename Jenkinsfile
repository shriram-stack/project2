pipeline {
    agent any
    environment {
        IMAGE = "shriram123/trend-app"
    }
    stages {
        stage('Build & Push') {
            steps {
                sh "docker build -t ${IMAGE}:latest ."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${IMAGE}:latest"
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                sh "aws eks update-kubeconfig --name trend-cluster --region ap-south-1"
                sh "kubectl apply -f k8s/deployment.yaml"
                sh "kubectl apply -f k8s/service.yaml"
                sh "kubectl rollout restart deployment/trend-app || true"
            }
        }
    }
    post {
        always {
            sh "kubectl get pods; kubectl get svc trend-service"
        }
    }
}
