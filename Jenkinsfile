pipeline {
  agent any
  tools { 
        maven 'Maven'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Docker Build') {
      steps {
        sh '/usr/bin/docker build -t employee-service .'
      }
    }   
    stage('push image to ECR'){
      steps {
        withDockerRegistry(credentialsId: 'ecr:us-east-1:aws-credentials', url: 'http://296217409404.dkr.ecr.us-east-1.amazonaws.com/employee-service') {
          sh 'docker tag employee-service:latest 296217409404.dkr.ecr.us-east-1.amazonaws.com/employee-service:latest'
         sh 'docker push 296217409404.dkr.ecr.us-east-1.amazonaws.com/employee-service:latest'
        } 
      }
    }
    
    stage('deploy to EKS Cluster') {
      steps {
      node('eks-cluster'){    
        checkout scm
        sh 'aws eks --region us-east-1 update-kubeconfig --name terraform-eks-demo'
        sh '/home/ec2-user/bin/kubectl apply -f deployment.yaml'
        sh '/home/ec2-user/bin/kubectl apply -f service.yaml'
        }
      }
    }
  }
}
