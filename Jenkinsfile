pipeline {
    agent any
    
    environment{
        AWS_ACCOUNT_ID = '56150076****' 
        REPOSITORY_URI = '56150076****.dkr.ecr.us-east-1.amazonaws.com/users'
        IMAGE_1_NAME = 'users'
        IMAGE_2_NAME = 'shift'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        AWS_DEFAULT_REGION = 'us-east-1'
        IMAGE_REPO_NAME = 'users'
        EKS_CLUSTER_NAME = "my-eks-cluster"
        KUBECONFIG = "${env.JENKINS_HOME}/.kube/config"
    }
    tools {
       maven 'localMaven'
       jdk 'localJdk'
    }

    stages {
        stage('Git checkout') {
            steps {
                echo 'Cloning project repo'
                git branch: 'main', url: 'https://github.com/sosocookeygam/EKS-automation.git'
                sh 'ls'
            }
        }
        stage('Build') {
            steps {
        sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                 docker.build("users:${env.BUILD_NUMBER}", "-f Dockerfile.users .")
                 docker.build("shift:${env.BUILD_NUMBER}", "-f Dockerfile.shift .")
                }
            }
        } 
        stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }
                 
            }
        }
        stage('Pushing to ECR') {
            steps{  
                script {
                sh "docker push ${REPOSITORY_URI}/${IMAGE_1_NAME}:latest"
                sh "docker push ${REPOSITORY_URI}/${IMAGE_2_NAME}:latest"
         }
        }
      }
        stage("Deploy to EKS") {
            steps {
                script{
                sh "aws eks update-kubeconfig --region us-east-1 --name ${EKS_CLUSTER_NAME}"
                sh "kubectl apply -f kubernetes/deployment.yaml"
                sh "kubectl rollout status deployment/${APP_NAME} --timeout=300s"
                }
            }
        }
    }
}


  








