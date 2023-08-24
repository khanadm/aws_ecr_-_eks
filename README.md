

# Demo Project: Complete CI/CD Pipeline with EKS and AWS ECR

## Technologies Used
- Kubernetes
- Jenkins
- AWS EKS (Elastic Kubernetes Service)
- AWS ECR (Elastic Container Registry)
- Java
- Maven
- Linux
- Docker
- Git

## Project Description

This project demonstrates the setup of a complete Continuous Integration and Continuous Deployment (CI/CD) pipeline using Kubernetes (EKS), Jenkins, AWS ECR, and other related technologies. The goal is to automate the build, testing, and deployment of a Java Maven application to an AWS EKS cluster.

### Project Steps

1. **Create Private AWS ECR Docker Repository**: Set up a private AWS Elastic Container Registry to store Docker images securely.

2. **Adjust Jenkinsfile**: Modify the Jenkinsfile to include the necessary steps for building and pushing Docker images to AWS ECR. This ensures that your application artifacts are stored in a secure and version-controlled registry.

3. **Integrate Deploying to K8s Cluster**: Integrate the deployment process into your CI/CD pipeline. This includes deploying your application to an AWS EKS cluster from the AWS ECR private registry. This step ensures that your application is automatically deployed to your Kubernetes cluster whenever a new version is built and pushed to AWS ECR.

### Configuration Overview

The complete CI/CD project consists of the following configuration steps:

#### CI Step: Increment Version

In this step, the project version is incremented to ensure that each build has a unique version identifier.

#### CI Step: Build Artifact for Java Maven Application

This step involves building the Java Maven application. It ensures that the application is compiled, tested, and packaged into an artifact ready for deployment.

#### CI Step: Build and Push Docker Image to AWS ECR

The Docker image for the application is built and pushed to the private AWS ECR Docker repository. This image contains the application code and its dependencies.

#### CD Step: Deploy New Application Version to EKS Cluster

This step deploys the newly built Docker image to the AWS EKS cluster, ensuring that the latest version of the application is running in the Kubernetes environment.

#### CD Step: Commit the Version Update

Once the deployment is successful, the version update is committed to your version control system (e.g., Git). This step helps in tracking changes and maintaining a history of deployments.

 # Setup Guide for CI/CD Pipeline with EKS and AWS ECR

This guide will walk you through the setup process for a complete CI/CD pipeline using Kubernetes (EKS), Jenkins, AWS ECR, and related technologies.

## Step 1: Install kubectl


**Download kubectl**


```sh
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.21.2/2021-07-05/bin/linux/amd64/kubectl
```

**Make it executable**

```sh
chmod +x ./kubectl
```

**Move it to a directory in your PATH**

```sh
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
```

**Add it to your shell's profile (optional, for persistence)**

```sh
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
```

**Verify kubectl installation**
```sh
kubectl version --short --client
```



**Install eksctl**


Install eksctl, a tool for creating and managing Amazon EKS clusters:



```sh
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```



```sh
mv /tmp/eksctl /usr/local/bin
```

```sh
eksctl version
```


# Install Docker Ubuntu | How to install Docker in Ubuntu 22.0.4 | Setup Docker on Ubuntu



**Step 1: Update Local Packages**



```sh
sudo apt update
```


**Install docker**

```sh
sudo apt install docker.io -y
```


**Add Ubuntu user to Docker group**

```sh
sudo usermod -aG docker $USER
```

The Docker service needs to be setup to run at startup. To do so, type in each command followed by enter:


```sh
sudo systemctl start docker
```


```sh
sudo systemctl enable docker
```


```sh
sudo systemctl status docker
```


# How to Install Maven on Linux (Ubuntu)


**Step 1: Download the Maven Binaries**


```sh
wget https://mirrors.estointernet.in/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```

```sh
tar -xvf apache-maven-3.6.3-bin.tar.gz
```

```sh
mv apache-maven-3.6.3 /opt/
```


**Step 2: Setting M2_HOME and Path Variables**

```sh
M2_HOME='/opt/apache-maven-3.6.3'
PATH="$M2_HOME/bin:$PATH"
export PATH
```


**Step 3: Verify the Maven installation**

Execute ```mvn -version``` command and it should produce the following output.



```$ mvn -version
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /opt/apache-maven-3.6.3
Java version: 13.0.1, vendor: Oracle Corporation, runtime: /opt/jdk-13.0.1
Default locale: en, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-47-generic", arch: "amd64", family: "unix"
```



# AWS ECR Repository Setup and IAM Role Configuration

This guide outlines the steps to create a repository in AWS Elastic Container Registry (ECR), create an IAM role with `ContainerRegistryFullAccess`, and attach that IAM role to an EC2 instance.

## Step 1: Create an ECR Repository

1. Log in to the AWS Management Console.

2. Navigate to the Amazon Elastic Container Registry (ECR) service.

3. In the ECR dashboard, choose "Repositories" from the left-hand menu.

4. Click the "Create repository" button.

5. Provide a name for your repository and configure any additional settings as needed (e.g., scan on push, encryption).

6. Click the "Create repository" button to create your ECR repository.

## Step 2: Create an IAM Role

1. Navigate to the AWS Identity and Access Management (IAM) service in the AWS Management Console.

2. In the IAM dashboard, choose "Roles" from the left-hand menu.

3. Click the "Create role" button.

4. Choose the service that will use this role, in this case, select "EC2."

5. In the permissions policy, search for and select the policy named "AmazonEC2ContainerRegistryFullAccess." This policy grants the necessary permissions for ECR.

6. Continue through the role creation process, providing a name and any additional tags or details as needed.

7. Review your configuration and click the "Create role" button to create the IAM role.

## Step 3: Attach the IAM Role to an EC2 Instance

1. Launch or identify the EC2 instance to which you want to attach the IAM role.

2. In the EC2 dashboard, select the instance and click the "Actions" button.

3. Choose "Instance Settings" and then click "Attach/Replace IAM Role."

4. Select the IAM role you created earlier (with `ContainerRegistryFullAccess`) from the dropdown menu.

5. Click the "Apply" button to attach the IAM role to the EC2 instance.



# Installing Required Jenkins Plugins


1. Log in to your Jenkins dashboard.

2. In the Jenkins dashboard, click on "Manage Jenkins" on the left-hand menu.

3. Click on "Manage Plugins."

4. Search for "Docker","Docker CLI", "Kubernetes CLI" and select the all plugin.

5. Click "Install without restart" to install the selected plugins.

6. Once the installation is complete, Jenkins will display a success message.

7. You may need to restart Jenkins for the changes to take effect. If so, click the "Restart Jenkins when the installation is complete and no jobs are running" checkbox and wait for the restart to finish.




**Now to create an Amazon EKS cluster, use the following command template:**


Now switch user to jenkins


```sh
sudo su - jenkins
```


```sh
eksctl create cluster --name CLUSTER_NAME --region REGION --version K8S_VERSION --nodegroup-name NODEGROUP_NAME --node-type NODE_INSTANCE_TYPE --nodes NODE_COUNT
```



Now run this command to see node

```sh
kubectl get nodes
```


Now we have to write Jenkins pipeline


```sh
pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "377178311198"
        AWS_DEFAULT_REGION = "ap-south-1"
        IMAGE_REPO_NAME = "java_application"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
        stage('Logging into AWS ECR') {
            steps {
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
            }
        }

        stage('Build Maven') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/khanadm/aws_ecr_-_eks.git']])
                sh 'mvn clean package'
                sh 'mvn install'
            }
        }

        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Pushing to ECR') {
            steps {
                script {
                    sh "docker tag ${IMAGE_REPO_NAME}:${BUILD_NUMBER} ${REPOSITORY_URI}:${BUILD_NUMBER}"
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('k8s deployment') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', contextName: '', credentialsId: 'k88s', restrictKubeConfigAccess: true, serverUrl: '') {
                        sh 'ls -l'
                        sh "export BUILD_NUMBER=${BUILD_NUMBER} && envsubst < deploymentservice.yaml > deploymentservice_updated.yaml"
                        sh 'kubectl apply -f deploymentservice_updated.yaml'
                    }
                }
            }
        }
    }
}
```

Now run this command to get pods



```sh
kubectl get pods
```

