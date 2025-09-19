pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/shankutanna/Devops.git'
        EMAIL    = 'umatanna9@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                // Pull code from your GitHub repo
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Artifact') {
            steps {
                // Zip the app folder into build.zip
                sh 'zip -r build.zip app/'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'build.zip', fingerprint: true
            }
        }

        stage('Notification') {
            steps {
                script {
                    echo "✅ Build #${env.BUILD_NUMBER} of Job ${env.JOB_NAME} completed successfully."

                    // Send email on success (requires Email Extension plugin configured in Jenkins)
                    emailext(
                        to: "${EMAIL}",
                        subject: "SUCCESS: Job '${env.JOB_NAME}' Build #${env.BUILD_NUMBER}",
                        body: """
Hello,

The Jenkins build has completed *successfully*.

Job: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Git Repository: ${GIT_REPO}

Regards,  
Jenkins
"""
                    )
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Build FAILED: Job ${env.JOB_NAME} Build #${env.BUILD_NUMBER}"
            emailext(
                to: "${EMAIL}",
                subject: "FAILURE: Job '${env.JOB_NAME}' Build #${env.BUILD_NUMBER}",
                body: """
Hello,

The Jenkins build has *FAILED*.

Job: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Git Repository: ${GIT_REPO}

Please check console logs for details.

Regards,  
Jenkins
"""
            )
        }

        always {
            cleanWs()
        }
    }
}
