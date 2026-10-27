pipeline {
    agent any

    stages {
        stage('Validate Tools') {
            steps {
                bat 'docker --version'
                bat 'kubectl version --client'
            }
        }

        stage('Build Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
                    bat 'docker build -t %DOCKERHUB_USER%/set12-webapp:%BUILD_NUMBER% .'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
                    bat 'echo %DOCKERHUB_PASSWORD%| docker login -u "%DOCKERHUB_USER%" --password-stdin'
                    bat 'docker push %DOCKERHUB_USER%/set12-webapp:%BUILD_NUMBER%'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    ),
                    file(
                        credentialsId: 'kubeconfig-credentials',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat 'kubectl apply -f deployment.yaml'
                    bat 'kubectl set image deployment/set12-deployment nginx=%DOCKERHUB_USER%/set12-webapp:%BUILD_NUMBER%'
                    bat 'kubectl rollout status deployment/set12-deployment --timeout=180s'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kubeconfig-credentials',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat 'kubectl get deployment set12-deployment'
                    bat 'kubectl get pods -l app=set12-app'
                    bat 'kubectl get service set12-service'
                }
            }
        }
    }
}