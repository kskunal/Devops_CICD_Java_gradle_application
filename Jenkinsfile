pipeline{
    agent any
    stages{
        stage("sonar Quality check"){
            agent{
                docker{
                    image 'openjdk:11'
                }
            }
        steps{
            script{
                withSonarQubeEnv(credentialsId: 'sonar-token') {
                    sh 'chmod +x gradlew'
                    sh './gradlew sonarqube'
                }
                timeout(time:1, unit:"HOURS"){
                    def qg=waitForQualityGate()
                    if (qg.status != 'OK'){
                        error "Pipeline aborted due to Quality gate failure ${qg.status}"
                    }
                }
            }
        }
        }
    }
}