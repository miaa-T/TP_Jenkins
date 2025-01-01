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
        stage('Quality Gate Check') {
            steps {
                script {
                    // Retry logic to ensure SonarQube has completed analysis
                    def qualityGate = null
                    for (int i = 0; i < 5; i++) { // Retry 5 times
                        try {

                            qualityGate = waitForQualityGate()
                            echo  "status of quality gate : ${qualityGate?.status ?:}"
                            if (qualityGate == null || qualityGate.status == 'OK') break
                        } catch (Exception e) {
                            echo "Retrying Quality Gate Check... Attempt ${i + 1}"
                        }
                    }
                    if ( qualityGate.status != 'OK') {
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
        
    }
}
