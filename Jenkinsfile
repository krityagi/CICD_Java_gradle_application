pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
        NEXUS_URL = "34.125.222.46:8083"
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
                            docker build -t ${NEXUS_URL}/springapp:${VERSION} .
                            docker login -u admin -p $nexus_password ${NEXUS_URL}
                            docker push ${NEXUS_URL}/springapp:${VERSION}
                            echo "Deleting the docker image"
                            docker rmi ${NEXUS_URL}/springapp:${VERSION}

                        '''
                    }
                    
                }
            }
        }
       
    }

    post {
            always {
                mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "devopswork516@gmail.com";  
            }
        }
}