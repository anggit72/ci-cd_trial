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
        stage(‘Deploy Production’) {

            steps{

                git url: ‘https://github.com/viglesiasce/sample-app’

                step([$class: ‘KubernetesEngineBuilder’, 

                        projectId: “trial-velostrata”,

                        clusterName: “test”,

                        region: “asia-southeast1”,

                        manifestPattern: ‘k8s/production/’,

                        credentialsId: “gke”,

                        verifyDeployments: true])

            

        }
    }    
}
