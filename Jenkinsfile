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
                    def qualityGate = null
                    def maxAttempts = 10 // Nombre maximum de tentatives
                    def waitTime = 30 // Temps d'attente entre chaque tentative (en secondes)

                    for (int i = 0; i < maxAttempts; i++) {
                        try {
                            qualityGate = waitForQualityGate()
                            if (qualityGate.status == 'OK' || qualityGate.status == 'WARN') {
                                echo "Quality Gate passed with status: ${qualityGate.status}"
                                break
                            }
                        } catch (Exception e) {
                            echo "Retrying Quality Gate Check... Attempt ${i + 1}"
                        }
                        if (i < maxAttempts - 1) {
                            sleep(waitTime)
                        }
                    }

                    if (qualityGate == null || qualityGate.status == 'ERROR' || qualityGate.status == 'FAILED') {
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
