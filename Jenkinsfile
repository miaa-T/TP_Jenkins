pipeline {
    agent any
    environment {
        deployStatus = ''
    }
    stages {
        stage("test") {
            steps {
                bat './gradlew test'
                junit 'build/test-results/test/*.xml'
                cucumber buildStatus: 'UNSTABLE',
                         reportTitle: 'My report',
                         fileIncludePattern: 'target/report.json',
                         trendsLimit: 10
            }
        }
        stage("Code Analysis") {
            steps {
                withSonarQubeEnv('sonar') {
                    bat "./gradlew sonar"
                }
            }
        }
        stage('Quality Gate Check') {
            steps {
                script {
                    // Retry logic to ensure SonarQube has completed analysis
                    def qualityGate = null
                    for (int i = 0; i < 5; i++) { // Retry 5 times
                        try {
                            qualityGate = waitForQualityGate()
                            if (qualityGate.status == 'OK') break
                        } catch (Exception e) {
                            echo "Retrying Quality Gate Check... Attempt ${i + 1}"
                            sleep(10) // Wait for 10 seconds between retries
                        }
                    }
                    if (qualityGate == null || qualityGate.status != 'OK') {
                        error "Pipeline failed due to Quality Gate failure: ${qualityGate?.status ?: 'Unknown'}"
                    }
                }
            }
        }
        stage("Build") {
            steps {
                bat './gradlew build'
                bat './gradlew javadoc'
                archiveArtifacts 'build/libs/*.jar'
            }
        }
        stage("Deploy") {
            steps {
                script {
                    bat './gradlew publish'
                }
            }
            post {
                failure {
                    script {
                        deployStatus = 'failure'
                    }
                }
                success {
                    script {
                        deployStatus = 'success'
                    }
                }
            }
        }
        stage("Notification") {
            steps {
                notifyEvents message: deployStatus, token: 'yr39rpilgauqk4ryend8tulapje7cb36'
                mail to: 'km_hathat@esi.dz',
                     subject: "Deployment ${deployStatus}",
                     body: "Deployment status: ${deployStatus}"
            }
        }
    }
}
