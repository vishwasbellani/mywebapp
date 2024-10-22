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

        stage('Authenticate with GCP') {
    steps {
      script {
        sh '''
          echo Using credential file at: $GOOGLE_APPLICATION_CREDENTIALS
          # Your other GCP commands using authorized credentials
        '''
      }
    }
  }


        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    // Tag the image
                    sh 'docker tag your-node-app:1.0.0 <asia-south1>-docker.pkg.dev/<vishwas24>/<mynodeapp>/your-node-app:1.0.0'
                    
                    // Push the image to GCP Artifact Registry
                    sh 'docker push <asia-south1>-docker.pkg.dev/<vishwas24>/<mynodeapp>/your-node-app:1.0.0'
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images after the build
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