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
                            echo 'execute permission to gradlew'                   
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube --warning-mode all'
                    }

                }
            }
        }
    }
}