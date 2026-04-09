#!/usr/bin/env groovy

pipeline {
    agent any
    parameters {
        string(name: 'SERVER_URL', defaultValue: 'https://825a08c2-4596-4345-866d-f134a9c179a8.k8s.ondigitalocean.com')
    }
    stages {
        stage('build app') {
            steps {
               script {
                   echo "building the application..."
               }
            }
        }
        stage('build image') {
            steps {
                script {
                    echo "building the docker image..."
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo "deploying docker image..."
                    withKubeConfig([credentialsId: 'digitalocean', serverUrl: params.SERVER_URL]) {
                        sh 'kubectl create deployment nginx-deployment --image=nginx'
                    }
                }
            }
        }
    }
}
