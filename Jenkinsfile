pipeline {
    agent any

    stages {


        stage('Verify Webhook') {
            steps {
                echo 'GitHub webhook triggered Jenkins successfully!'
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh './gradlew test' // Runs the tests defined in your project
            }
        }
        stage('Archive Test Results') {
            steps {
                junit 'build/test-results/test/*.xml' // Archive test reports in Jenkins
            }
        }
        stage('Cucumber Reports') {
            steps {
                cucumber 'build/reports/cucumber.json' // Use the Cucumber plugin in Jenkins
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
