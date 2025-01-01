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

         /* stage("Code Quality") {
                          steps {
                          sleep(10)
                             timeout(time: 1, unit: 'HOURS') {
                                             waitForQualityGate abortPipeline: true
                                           }
                          }
                      }*/
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
                mail to: 'km_toubal@esi.dz',
                     subject: "Deployment ${deployStatus}",
                     body: "Deployment status: ${deployStatus}"
            }
        }
    }
}
