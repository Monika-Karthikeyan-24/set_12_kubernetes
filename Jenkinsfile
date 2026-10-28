stage('Build Docker Image') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKERHUB_USER',
                passwordVariable: 'DOCKERHUB_PASSWORD'
            )
        ]) {
            bat 'if "%DOCKERHUB_USER%"=="" exit /b 1'
            bat 'echo Docker Hub username is configured'
            bat 'docker build -t %DOCKERHUB_USER%/set12-webapp:%BUILD_NUMBER% .'
        }
    }
}
