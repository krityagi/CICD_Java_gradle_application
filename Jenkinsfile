pipeline{
    agent any
    stages{
        stage("Sonar quality Check"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Sonar-token') {       
                                           
                            sh 'chmod +x gradlew'
                            sh './gradlew wrapper --gradle-version 7.1.1'
                            sh './gradlew sonarqube'
                    }

                }
            }
        }
    }
}