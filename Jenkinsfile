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

                    bat "./gradlew sonar"

            }
        }


      stage("Code Quality") {
                  steps {
                      waitForQualityGate abortPipeline: true
                  }
              }

    }

