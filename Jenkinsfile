pipeline {
    agent any

    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-credentials') // Ensure this matches your credentials ID
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Building the Docker image
                    sh 'docker build -t your-node-app:1.0.0 .'
                }
            }
        }

        stage('Authenticate with GCP') {
            steps {
                withCredentials([file(credentialsId: 'gcp-credentials', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    script {
                        // Activating service account
                        sh 'gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}'
                        sh 'gcloud config set project vishwas24'
                        sh 'gcloud config get-value project'
                    }
                }
            }
        }

        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    // Tagging the Docker image
                    sh 'docker tag your-node-app:1.0.0 asia-south1-docker.pkg.dev/vishwas24/mynodeapp/your-node-app:1.0.0'

                    // Pushing the Docker image to Artifact Registry
                    sh 'docker push asia-south1-docker.pkg.dev/vishwas24/mynodeapp/your-node-app:1.0.0'
                }
            }
        }
    }

    post {
        always {
            // Clean up after the build
            cleanWs()
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
