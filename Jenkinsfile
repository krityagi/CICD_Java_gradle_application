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
        stage('identifying misconfigs using datree in helm charts'){
            steps{
                script{
                    dir('kubernetes/') {
                        sh '''
                            whoami
                        '''
                    }
                }
            }
        }
        stage("pushing the helm charts to nexus"){
            steps{
                script{
                    withCredentials([string(credentialsId:'nexus_creds', variable: 'nexus_password')]) {
                        dir('kubernetes/') {
                        sh '''
                            helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                            tar -czvf myapp-${helmversion}.tgz myapp/

                            curl -u admin:$nexus_password http://34.125.222.46:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v

                        '''
                        }
                    }
                    
                }
            }
        }
        stage('Deploying application ok K8s cluster') {
            steps {
                script{
                    withCredentials([file(credentialsId: 'kube', variable: 'KUBECONFIG')]) {
                        dir ("kubernetes/"){  
                            sh 'helm list'
                            sh 'helm upgrade --install --set image.repository="34.125.222.46:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
                        }
                    }
                }
            }
        }
       
    }

    post {
            always {
                mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "devopswork516@gmail.com";  
                sh 'echo "Email has been sent"'
            }
        }
}