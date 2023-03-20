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
                            sh 'gradle --stop'
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }

                }
            }
        }
    }
}