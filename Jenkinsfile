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
                sh 'mvn clean verify -DskipUnitTests'
            }
        }

    }
}