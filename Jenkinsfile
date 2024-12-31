pipeline {
    agent any

    stages {
       stage('Run Unit Tests') {
           steps {
               sh './gradlew test --continue'
           }
       }

        stage('Archive Test Results') {
            steps {
                junit 'build/test-results/test/*.xml'
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
