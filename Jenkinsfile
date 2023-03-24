pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("Sonar quality Check"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Sonar-token') {       
                                           
                            sh 'chmod +x gradlew'
                            sh './gradlew wrapper --gradle-version 7.1.1'
                            sh './gradlew sonarqube'
                    }

                    timeout(5) {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }

                }
            }
        }
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId:'nexus_creds', variable: 'nexus_password')]) {
                        sh '''
                            docker build -t 34.125.9.155:8083/springapp:${VERSION} .
                            docker login -u admin -p $nexus_password 34.125.9.155:8083
                            docker push 34.125.9.155:8083/springapp:${VERSION}
                            docker rmi 34.125.9.155:8083/springapp:${VERSION}

                        '''
                    }
                    
                }
            }
        }
    }
}