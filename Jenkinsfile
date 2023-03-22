pipeline{
    agent any
    stages{
        stage("Sonar quality Check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Sonar-token') {       
                                           
                            sh 'chmod +x gradlew'
                            sh './gradlew wrapper --gradle-version 8.0.2'
                            sh './gradlew sonarqube'
                    }

                }
            }
        }
    }
}