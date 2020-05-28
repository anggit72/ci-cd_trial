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
        stage('Approval') {
            // no agent, so executors are not used up when waiting for approvals
            agent none
            steps {
                script {
                    def deploymentDelay = input id: 'Deploy', message: 'Deploy to production?', submitter: 'rkivisto,admin', parameters: [choice(choices: ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', '13', '14', '15', '16', '17', '18', '19', '20', '21', '22', '23', '24'], description: 'Hours to delay deployment?', name: 'deploymentDelay')]
                    sleep time: deploymentDelay.toInteger(), unit: 'MINUTS'
                }
            }
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
