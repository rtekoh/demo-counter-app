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
        stage('Quality Gate Status'){

            steps{
                waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-apikey'
            }
        }

        stage ('Upload Var file to Nexus'){

            steps{
                script{
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', file: 'target/Uber.jar', 
                            type: 'jar'
                            ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '44.214.211.83:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demoapp-release', 
                    version: '1.0.0'
                }
            }
        }
    }
}