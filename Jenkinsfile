pipeline {
    agent any

    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-credentials')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/vishwasbellani/mywebapp.git'
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
                withCredentials([file(credentialsId: 'gcp-credentials', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh 'gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"'
                    sh 'gcloud config set project vishwas24'
                    echo "Current Google Cloud Project: ${sh(script: 'gcloud config get-value project', returnStdout: true).trim()}"
                }
            }
        }

        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    docker.withRegistry('https://asia-south1-docker.pkg.dev', 'gcp-credentials') {
                        docker.image('your-node-app:1.0.0').push('1.0.0')
                    }
                }
            }
        }
    }
}

