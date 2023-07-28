pipeline {
    agent any 

    stages{
        
        stage('Git Checkout'){

            steps{
                git branch: 'main', url: 'https://github.com/rtekoh/demo-counter-app.git'
            }
        }

        stage('Unit Testing'){

            steps{
                sh 'mvn test'
            }
        }

        stage('Integration Testing'){

            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('maven build'){

            steps{
                sh 'mvn clean install'
            }
        }

        stage('Static Code Analysis'){

            steps{
                script {
                withSonarQubeEnv(credentialsId: 'Sonar-apikey') {
                    sh 'mvn clean package sonar:sonar'
                 }
                }
            }
        }
    }
}