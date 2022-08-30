pipeline{
    agent any
    environment{
        VERSION="${env.BUILD_ID}"
    }
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
        stage("docker build & docker push"){
            steps{
                script{
                    sh '''
                        docker build -t localhost:8083/springapp:${VERSION}
                        docker login -u admin $docker_password localhost:8083
                        docker push localhost:8083/springapp:${VERSION}
                        docker rmi localhost:8083/springapp:${VERSION}
                    '''
                }
            }
        }
    }
}