pipeline {
    agent any
    environment {
        PROJECT_ID = 'trial-velostrata'
        CLUSTER_NAME = 'test'
        LOCATION = 'asia-southeast1'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("anggit/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        myapp.push("${env.BUILD_ID}")    
                        myapp.push("latest")
                    }
                }
            }
        }        
        stage('Deploy to GKE staging') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                sh "kubectl config use-context gke_trial-velostrata_asia-southeast1_test"
                sh "kubectl apply -f /var/lib/jenkins/workspace/hello/deployment.yaml"
            }
        }
        stage('Deploy to EKS staging') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                sh "kubectl config use-context arn:aws:eks:ap-southeast-1:485418931031:cluster/test"
                sh "kubectl apply -f /var/lib/jenkins/workspace/hello/deployment.yaml"
            }
        }
        timeout(time: 1, unit: "MINUTES") {
            input message: 'Do you want to approve the deploy in production?', ok: 'Yes'
        }
        stage('Deploy to GKE Prod') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                sh "kubectl config use-context gke_trial-velostrata_asia-southeast1_prod"
                sh "kubectl apply -f /var/lib/jenkins/workspace/hello/deployment.yaml"
            }
        }    
        stage('Deploy to EKS Prod') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                sh "kubectl config use-context arn:aws:eks:ap-southeast-1:485418931031:cluster/prod"
                sh "kubectl apply -f /var/lib/jenkins/workspace/hello/deployment.yaml"
            }
        }         
   } 
}
