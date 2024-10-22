pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/vishwasbellani/mywebapp.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("your-node-app:1.0.0")
                }
            }
        }
        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-service-account', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                    sh 'gcloud config set project vishwas24'
                }
            }
        }
        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    docker.withRegistry('https://gcr.io', 'gcp-service-account') {
                        docker.image('your-node-app:1.0.0').push('latest')
                    }
                }
            }
        }
    }
}