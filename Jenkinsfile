pipeline {
    agent any

    stages {


        stage('Verify Webhook') {
            steps {
                echo 'GitHub webhook triggered Jenkins successfully!'
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed.'
        }
    }
}
