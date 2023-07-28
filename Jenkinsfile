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
                    def readPomVersion = readMavenPom file: 'pom.xml'
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshots" : "demoapp-release"
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
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"
                }
            }
        }
        stage('Docker Image Build'){

            steps{
                sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID rtekoh/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID rtekoh/$JOB_NAME:latest'
            }
        }

        stage('Push images to DockerHub'){

            steps{
                
                script{
                    withCredentials([string(credentialsId: 'dockerhub_passd', variable: 'dockerhub_passd')]) {
                       sh 'docker login -u rtekoh -p ${dockerhub_passd}'
                       sh 'docker image push rtekoh/$JOB_NAME:v1.$BUILD_ID'
                       sh 'docker image push rtekoh/$JOB_NAME:latest'
                       sh 'docker image rm rtekoh/$JOB_NAME:latest rtekoh/$JOB_NAME:v1.$BUILD_ID $JOB_NAME:v1.$BUILD_ID'
                 }
                }
            }
        }
    }
}