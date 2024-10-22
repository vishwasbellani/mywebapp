pipeline {
    agent any // Use any available agent to run the pipeline
    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-credentials') // Use the correct credentials ID
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
                    // Build Docker image
                    docker.build("your-node-app:1.0.0")
                }
            }
        }
        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-credentials', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    // Print the path for debugging
                    sh 'echo "Using credential file at: $GOOGLE_APPLICATION_CREDENTIALS"'
                    
                    // Authenticate with the GCP service account
                    sh 'gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"'
                    
                    // Set the GCP project ID
                    sh 'gcloud config set project vishwas24'
                    
                    // Check the currently set project
                    echo "Current Google Cloud Project: ${sh(script: 'gcloud config get-value project', returnStdout: true).trim()}"
                }
            }
        }
        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                withCredentials([file(credentialsId: 'gcp-credentials', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    // Tag and push the Docker image
                    sh 'docker tag your-node-app:1.0.0 asia-south1-docker.pkg.dev/vishwas24/mynodeapp/your-node-app:1.0.0'
                    sh 'docker push asia-south1-docker.pkg.dev/vishwas24/mynodeapp/your-node-app:1.0.0'
                }
            }
        }
    } // Close stages
} //