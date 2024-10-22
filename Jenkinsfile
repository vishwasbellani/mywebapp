pipeline {
    agent any

    environment {
        PROJECT_ID = 'vishwas24'
        REGION = 'asia-south1' // Corrected region
        REPOSITORY_NAME = 'mynodeapp' // Name of your artifact registry repository
        IMAGE_NAME = 'your-node-app'
        SERVICE_ACCOUNT_KEY = 'ec01beac71f1d1fd77ad67ccf9162b4959eea37a' // Jenkins credential ID for GCP service account key
    }

    stages {
        stage('Print Environment Variables') {
            steps {
                script {
                    // Print the PATH variable to check for Git location
                    sh 'echo $PATH'
                }
            }
        }

    

        stage('Checkout Code') {
            steps {
                // Checkout the Node.js app source code
                git 'https://github.com/vishwasbellani/mywebapp.git' // Use your actual repository URL
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Optionally remove existing node_modules if needed
                    sh 'rm -rf node_modules'
                    
                    // Increase npm install timeout and log output
                    timeout(time: 30, unit: 'MINUTES') {
                        sh 'npm install --verbose'
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                // Run tests (optional, modify if needed)
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Extract version from package.json
                    def appVersion = sh(script: "cat package.json | grep version | awk -F '\"' '{print \$4}'", returnStdout: true).trim()

                    // Build the Docker image
                    docker.build("${IMAGE_NAME}:${appVersion}")
                }
            }
        }
stage('Test Credentials') {
    steps {
        script {
            echo "Available credentials:"
            sh 'echo $GCP_KEYFILE' // This should show the path or an empty string
        }
    }
}
        stage('Authenticate with GCP') {
    steps {
        script {withCredentials([file(credentialsId: 'YOUR_CREDENTIALS_ID', variable: 'GCP_KEYFILE')]) {
                // Authenticate with Google Cloud using the service account key
                sh '''
                gcloud auth activate-service-account --key-file=$GCP_KEYFILE
                gcloud config set project ${PROJECT_ID}
                gcloud auth configure-docker ${REGION}-docker.pkg.dev
                '''
            }
        }
    }
}
        stage('Tag & Push to GCP Artifact Registry') {
            steps {
                script {
                    // Extract version again (not ideal, but it's necessary here)
                    def appVersion = sh(script: "cat package.json | grep version | awk -F '\"' '{print \$4}'", returnStdout: true).trim()

                    // Tag the Docker image for Artifact Registry
                    sh "docker tag ${IMAGE_NAME}:${appVersion} ${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPOSITORY_NAME}/${IMAGE_NAME}:${appVersion}"

                    // Push the Docker image to GCP Artifact Registry
                    sh "docker push ${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPOSITORY_NAME}/${IMAGE_NAME}:${appVersion}"
                }
            }
        }
    }

    post {
        always {
            // Clean up the workspace after the pipeline completes
            cleanWs()
        }
    }
}
