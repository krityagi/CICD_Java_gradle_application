pipeline{
    agent any
    stages{
        stage("Sonar quality Check"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Sonar-token') {       
                            echo 'execute permission to gradlew'                   
                            sh 'chmod +x gradlew'
                            sh './gradlew --stop'
                            sh './gradlew sonarqube --debug output'
                    }

                }
            }
        }
    }
}