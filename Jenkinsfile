cd ~/project2/Trend
cat > Jenkinsfile << 'EOF'
pipeline {
    agent any
    environment {
        IMAGE = "shriram123/trend-app"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build & Push Docker') {
            steps {
                dir('.') {
                    sh "docker build -t ${IMAGE}:latest ."
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push ${IMAGE}:latest"
                    }
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                sh '''
                aws eks update-kubeconfig --name trend-cluster --region ap-south-1
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                kubectl rollout restart deployment/trend-app || true
                echo "Live URL:"
                kubectl get svc trend-service
                '''
            }
        }
    }
}
EOF

git add Jenkinsfile
git commit -m "Final bulletproof Jenkinsfile"
git push origin main
