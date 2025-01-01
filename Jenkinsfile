pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                bat 'gradlew.bat test'
                junit 'build/reports/tests/test/*.xml' // Archivage des résultats
            }
        }

        stage('Cucumber Reports') {
            steps {
                cucumber 'build/reports/cucumber.json'
            }
        }
        stage('Notify Slack') {
            steps {
                slackSend(
                    channel: '#general',
                    message: "Build completed successfully!",
                    webhookToken: 'https://hooks.slack.com/services/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'
                )
            }
        }
        stage('Send Email') {
            steps {
                mail(
                    to: 'mahdia.toubal@gmail.com',
                    subject: 'Build Notification',
                    body: 'The build has completed successfully!'
                )
            }
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
