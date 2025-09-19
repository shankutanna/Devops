pipeline {
    agent any

    environment {
        NODE_HOME = '/usr/bin' // adjust if needed
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Debug Workspace') {
            steps {
                echo "Listing workspace contents after checkout..."
                sh 'ls -l'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing npm dependencies...'
                script {
                    if (!fileExists('package.json')) {
                        error "package.json not found! Cannot install npm dependencies."
                    }
                    sh '''
                        if ! command -v npm &> /dev/null; then
                            echo "npm not found! Please install Node.js and npm on the agent."
                            exit 1
                        fi
                        npm install
                    '''
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh 'npm test'
            }
        }

        stage('Build Artifact') {
            steps {
                script {
                    def folderToZip = 'app'
                    if (!fileExists(folderToZip)) {
                        echo "Folder '${folderToZip}' not found! Zipping entire repository instead."
                        folderToZip = '.'
                    } else {
                        echo "Zipping folder '${folderToZip}'..."
                    }

                    sh '''
                        if ! command -v zip &> /dev/null; then
                            echo "zip command not found! Please install zip on the agent."
                            exit 1
                        fi
                        zip -r build.zip ${folderToZip}
                    '''
                }
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'build.zip', allowEmptyArchive: true
            }
        }

        stage('Notification') {
            steps {
                echo 'Sending notifications...'
                // You can configure emailext or Slack here
            }
        }
    }

    post {
        always {
            cleanWs()
            echo "✅ Workspace cleaned"
        }

        success {
            echo "✅ Build PASSED!"
        }

        failure {
            echo "❌ Build FAILED!"
        }
    }
}
