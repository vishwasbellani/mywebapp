pipeline {
    agent any

    environment {
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-credentials') // Reference to the Jenkins credential for GCP
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout code from Git repository
                git url: 'https://github.com/vishwasbellani/mywebapp.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t your-node-app:1.0.0 .'
                }
            }
        }

        sstage('Authenticate with GCP') {
    steps {
        script {
            // Activate the GCP service account
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
            // Replace with your actual values
            def gcpRegion = 'asia-south1'
            def projectId = 'vishwas24'
            def repository = 'mynodeapp'

            // Tag the image
            sh "docker tag your-node-app:1.0.0 ${gcpRegion}-docker.pkg.dev/${projectId}/${repository}/your-node-app:1.0.0"
            
            // Push the image to GCP Artifact Registry
            sh "docker push ${gcpRegion}-docker.pkg.dev/${projectId}/${repository}/your-node-app:1.0.0"
        }
    }
}
    }

    post {
        always {
            
            sh 'docker rmi your-node-app:1.0.0 || true'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}