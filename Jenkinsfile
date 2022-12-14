pipeline {
    agent any
    environment {
        PROJECT_ID = 'extreme-torch-358604'
        CLUSTER_NAME = 'jenkins'
        LOCATION = 'us-west4-b'
        CREDENTIALS_ID = 'kubernetes'
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
                    sh "docker build -t dockkunal/hello:${env.BUILD_ID} ."
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    withCredentials( \
                                 [string(credentialsId: 'dockerhub',\
                                 variable: 'dockerhub')]) {
                        sh "docker login -u dockkunal -p ${dockerhub}"
                    }
                    sh "docker push dockkunal/hello:${env.BUILD_ID}"
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}
