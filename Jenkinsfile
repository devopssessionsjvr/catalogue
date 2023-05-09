pipeline {
  agent any
  parameters{
        string(name: 'VERSION', description: 'Enter the APP VERSION')
    }
environment{
        AWS_ACCOUNT_ID="360921704373"
        REGION="ap-south-1"
        REPO_URI="${AWS_ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/catalogue"
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REGISTRY_CREDENTIALS = 'docker-creds'
       
    }
  stages {
    stage('Clone') {
        steps{
        script{
                    echo "Clone started"
                    gitInfo = checkout scm
                            
        }
      }
    }

    stage('Docker build'){
            steps{
                script{                  
                        sh """
                         docker build -t catalogue:${VERSION} .
                        """
                }
            }
        }
     stage('Image push to Docker Hub'){
            steps{
                script{
                        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDENTIALS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    
                        sh """
                        docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                        docker tag catalogue:${VERSION}  jaydocker2024/catalogue:${VERSION}
                        docker push jaydocker2024/catalogue:${VERSION}
                        """
                    
                     }
                
                }
            }
     }
       stage('Deploy to EKS'){
            steps{
                
              withAWS(credentials: 'aws-auth', region: 'ap-south-1', policy: 'AmazonEC2FullAccess' 
    
        credentialsId: 'aws-auth',
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
    
)
          
              script{
                        sh """
                        aws eks update-kubeconfig --name firts-cluster --region ap-south-1 --kubeconfig kubeconfig
                        kubectl apply -f pod.yaml --kubeconfig kubeconfig
                        """
                    
                     }
                
                }
            }
       }
  }
  
