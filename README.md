# EKS-automation

This project demonstrates how to deploy two containers that scale independently from one another in an AWS EKS cluster
Prerequisites:
AWS account
Jenkins server with the necessary plugins
Github account  

STEPS:
I have added a Jenkins file to the root level directory of the repository. Jenkins needs to be set up with your AWS credentials and the necessary IAM roles to access your AWS ECR

The Jenkinsfile consist of different stages as follows:
Git checkout: to fetch the codes from Github

Build stage: using maven command to build the application code into a jar file. In this example I have used simple html files (users & shift directories) just to illustrate the process. In your work environment this could be java application codes or any other programming language

Build docker image: here we reference the two docker files created in the root directory and run the command to build docker images

Logging and pushing into ECR: The Jenkins server then authenticates into the ECR repository and saves the images there.

Deploy to EKS: We assume that an EKS cluster is already created and referenced as an environment variable on the jenkinsfile. Jenkins then uses the yaml file in the repository to deploy the images as containers in the EKS cluster.

The secrets.yaml file is used to store the DB username and password (in base64 encoded format). This is then referenced in the deployment.yaml file

The deployment.yaml file is the configuration file which is used to manage the deployment and scaling of the containers.

The project also includes an RBAC.yaml file used to set up IAM controls to allow your development team to only deploy and rollback deployments in your AWS EKS cluster
