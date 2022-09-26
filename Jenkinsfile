pipeline {
    agent any
    
    environment {
        registry = "649562814399.dkr.ecr.us-east-1.amazonaws.com/my-python-repo:latest"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'c8e2e5b8-29f8-4459-b720-62a5057332d4', url: 'https://github.com/bisiakins/myPythonDockerRepo']]])
            }
        }
        stage ("Docker Build") {
          steps {
              script {
                  dockerImage = docker.build registry
              }
            }
        }
        stage ("Docker Push to ECR") {
            steps {
                script {
                   sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin  649562814399.dkr.ecr.us-east-1.amazonaws.com"
                   sh "docker push 649562814399.dkr.ecr.us-east-1.amazonaws.com/my-python-repo:latest"
                }
            }
        }
        stage ("Deply to Kubernetes Cluster") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                  sh 'kubectl apply -f eks-deploy-from-ecr.yaml'
                }
            }
        }
    }
}
always {
  // remove built docker image and prune system
  print 'Cleaning up the Docker system.'
  sh 'docker system prune -f'
}

