pipeline {
    agent any

    environment {
        APP_NAME = "my-node-app"
        BUILD_ARTIFACT = "build.zip"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/shankutanna/Devops.git'
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
                sh '''
                    zip -r ${BUILD_ARTIFACT} . \
                    -x "node_modules/*" \
                    -x ".git/*"
                '''
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: "${BUILD_ARTIFACT}", fingerprint: true
            }
        }
    }

    post {
        success {
            echo "✅ Build succeeded for ${APP_NAME}"
            echo "Slack notification: Build ${BUILD_NUMBER} succeeded!"
            echo "Email notification: Build succeeded! To: umatanna9@gmail.com"
        }
        failure {
            echo "❌ Build failed for ${APP_NAME}"
            echo "Email notification: Build ${BUILD_NUMBER} failed! To: umatanna9@gmail.com"
        }
    }
}

