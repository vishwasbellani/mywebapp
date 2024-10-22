pipeline {
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
                    docker.withRegistry('https://asia-south1-docker.pkg.dev', 'gcp-credentials') {
                        // Push the Docker image with the specified version
                        docker.image('your-node-app:1.0.0').push('1.0.0')
                    }
                }
            }
        }

