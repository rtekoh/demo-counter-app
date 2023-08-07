pipeline{

    agent any

    parameters{
        choice(name: 'action', choices: 'create\ndestroy\ndestroyekscluster', description: 'Create/Update or Destroy the EKS cluster')
        string(name: 'cluster', defaultValue: 'counter-app', description: 'EKS cluster name')
        string(name: 'region', defaultValue: 'us-east-1', description: 'EKS cluster region')
    }

    stages{

        stage('Git Checkout'){
            
            steps{
                git branch: 'main', url: 'https://github.com/rtekoh/demo-counter-app.git'
            }

        }
        stage('EKS cluster Connect'){
            
            steps{
                sh """
                    aws eks --region ${params.region} update-kubeconfig --name ${params.cluster}
                    """;
            }

        }

        stage('EKS Deployment'){

            when { expression { params.action == 'create'}}
            steps{

                script{
                    def apply = false
                    try{
                        input message: 'Please confirm the apply to initiate the deployments', ok: 'Ready to apply the config'
                        apply = true
                    }
                    catch(err){
                        apply = false
                        CurrentBuild.result = 'UNSTABLE'
                    }
                    if(apply){
                        sh """
                        kubectl apply -f .
                                """
                    }
                }
                
            }
        }


    }

    
}