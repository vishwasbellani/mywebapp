pipeline {
    agent any
    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-credentials') // Your Jenkins credential ID
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git url: 'https://github.com/vishwasbellani/mywebapp.git'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t your-node-app:1.0.0 .'
                }
            }
        }
        stage('Authenticate with GCP') {
            steps {
                script {
                    sh '''
                        echo Using credential file at: $GOOGLE_APPLICATION_CREDENTIALS
                        gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"
                    '''
                }
            }
        }
        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    sh '''
                        docker tag your-node-app:1.0.0 asia-south1-docker.pkg.dev/vishwas24/mynodeapp/your-node-app:1.0.0
                        docker push asia-south1-docker.pkg.dev/vishwas24/mynodeapp/your-node-app:1.0.0
                    '''
                }
            }
        }
    }
    post {
        always {
            script {
                cleanWs()
            }
        }
    }
}