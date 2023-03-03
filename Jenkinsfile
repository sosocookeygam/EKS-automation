pipeline {
agent any
environment {
AWS_ACCOUNT_ID="561500766456"
AWS_DEFAULT_REGION= "us-east-1"
IMAGE_REPO_NAME = "users"
IMAGE_TAG= "latest"
REPOSITORY_URI = “${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}”
EKS_CLUSTER_NAME = "my-eks-cluster"
KUBECONFIG = "${env.JENKINS_HOME}/.kube/config"
}
}

stages{
    stage('Git checkout') {
            steps {
                echo 'cloning project codebase'
                git branch: 'main', url: 'https://github.com/sosocookeygam/airbnb-infrastructure.git'
                sh 'ls'
            }
        }

    stage('Build') {
        steps{
        script{
           dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}" -f Dockerfile.shift
           dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}" -f Dockerfile.users

        //    docker build -t my-users-image -f Dockerfile.users
        //    docker build -t my-shift-image -f Dockerfile.users
        }
        }
    }
    stage(‘Logging into AWS ECR’) {
        steps {
        script {
            sh "aws ecr get-login-password — region ${AWS_DEFAULT_REGION} | docker login — username AWS — password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
}

}
}

    stage('Pushing to ECR') {
        steps{
        script{
          sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URL}:$IMAGE_TAG"
          sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"

        //   docker push my-users-image
        //   docker push my-shift-image
        }
    }
}
 stage('Deploy') {
    steps{
        sh "kubectl apply -f users-deployment.yaml"
        sh "kubectl apply -f shift-deployment.yaml"
    }
 }


}




// https://medium.com/@vijulpatel865/building-docker-image-using-jenkins-pipeline-push-it-to-aws-ecr-aa02cc7a295e

// can i have two different docker files to create two different images in one directory?

// https://github.com/marketplace/actions/kubectl-aws-eks


// pipeline {
//   agent {
//     label "my-eks-agent"
//   }
//   environment {
//     APP_NAME = "my-app"
//     DOCKER_REGISTRY = "my-registry"
//     DOCKER_IMAGE = "${DOCKER_REGISTRY}/${APP_NAME}"
//     ECR_REGISTRY = "123456789012.dkr.ecr.us-east-1.amazonaws.com"
//     EKS_CLUSTER_NAME = "my-eks-cluster"
//     KUBECONFIG = "${env.JENKINS_HOME}/.kube/config"
//   }
//   stages {
//     stage("Build") {
//       steps {
//         sh "docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ."
//       }
//     }
//     stage("Push to ECR") {
//       steps {
//         withCredentials([[
//           credentialsId: 'aws-ecr-credentials',
//           accessKeyVariable: 'AWS_ACCESS_KEY_ID',
//           secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
//         ]]) {
//           sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
//           sh "docker tag ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ${ECR_REGISTRY}/${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
//           sh "docker push ${ECR_REGISTRY}/${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
//         }
//       }
//     }
//     stage("Deploy to EKS") {
//       steps {
//         sh "aws eks update-kubeconfig --region us-east-1 --name ${EKS_CLUSTER_NAME}"
//         sh "kubectl apply -f kubernetes/deployment.yaml"
//         sh "kubectl rollout status deployment/${APP_NAME} --timeout=300s"
//       }
//     }
//   }
//   post {
//     always {
//       sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
//       sh "docker rmi ${ECR_REGISTRY}/${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
//       sh "kubectl delete deployment ${APP_NAME}"
//       sh "kubectl delete service ${APP_NAME}"
//     }
//   }
// }

