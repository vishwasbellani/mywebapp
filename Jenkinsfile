pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                // Correcting the git step to include branch (if needed)
                git branch: 'master', url: 'https://github.com/vishwasbellani/mywebapp.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    docker.build("your-node-app:1.0.0")
                }
            }
        }
        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-credentials', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    // Authenticate with the GCP service account
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                    // Set the GCP project ID
                    sh 'gcloud config set project vishwas24'
                }
            }
        }
        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    // Use the GCP Artifact Registry URL (replace region and project-id accordingly)
                    docker.withRegistry('https://us-central1-docker.pkg.dev', 'gcp-credentials') {
                        // Push the Docker image with the 'latest' tag
                        docker.image('your-node-app:1.0.0').push('latest')
                    }
                }
            }
        }
    }
}
