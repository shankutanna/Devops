pipeline {
    agent any

    environment {
        // You can add environment variables here if needed
        NODE_ENV = 'production'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out the repository..."
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[url: 'https://github.com/shankutanna/Devops.git']]
                ])
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "Installing npm dependencies..."
                sh '''
                    if ! command -v npm &> /dev/null; then
                        echo "npm not found! Please install Node.js and npm on the agent."
                        exit 1
                    fi
                    npm install
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo "Running unit tests..."
                sh '''
                    if ! command -v node &> /dev/null; then
                        echo "Node.js not found! Cannot run tests."
                        exit 1
                    fi
                    npm test
                '''
            }
        }

        stage('Build Artifact') {
            steps {
                script {
                    def folderToZip = 'app'

                    if (fileExists(folderToZip)) {
                        echo "Zipping folder '${folderToZip}'..."
                        // Ensure zip is installed
                        sh '''
                            if ! command -v zip &> /dev/null; then
                                echo "zip command not found! Please install zip."
                                exit 1
                            fi
                        '''
                        sh "zip -r build.zip ${folderToZip}"
                    } else {
                        error "Folder '${folderToZip}' not found! Cannot create artifact."
                    }
                }
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'build.zip', fingerprint: true
            }
        }

        stage('Notification') {
            steps {
                echo "Sending build notification..."
                emailext (
                    subject: "Jenkins Build Notification: ${currentBuild.fullDisplayName}",
                    body: "Build ${currentBuild.fullDisplayName} finished with status: ${currentBuild.currentResult}\n\nCheck console output at ${env.BUILD_URL}",
                    to: "umatanna9@gmail.com"
                )
            }
        }

        stage('Debug Workspace') {
            steps {
                echo "Listing workspace contents for debugging..."
                sh 'ls -l'
            }
        }
    }

    post {
        always {
            cleanWs()
            echo "✅ Workspace cleaned"
        }
        success {
            echo "🎉 Build succeeded!"
        }
        failure {
            echo "❌ Build FAILED!"
        }
    }
}

