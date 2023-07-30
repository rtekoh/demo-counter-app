pipeline {
    agent any

    stages{

        stage("Git Checkout") {

            steps{
                git branch: 'main', url: 'https://github.com/rtekoh/demo-counter-app.git'
            }
        }
        stage('Unit Testing'){

            steps{
                sh 'mvn test'
            }
        }

        stage('Maven Integration Testing'){

            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Maven Build'){

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
        stage('Quality Gate Status'){

            steps{
                waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-apikey'
            }
        }
    }
}