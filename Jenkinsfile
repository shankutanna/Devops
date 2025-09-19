pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out the repository..."
                // Proper Git checkout
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[url: 'https://github.com/shankutanna/Devops.git']]
                ])
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
                echo "Installing npm dependencies..."
                sh '''
                    if [ -x "$(command -v npm)" ]; then
                        echo "npm found: $(which npm)"
                    else
                        echo "npm not found! Please install Node.js and npm on the agent."
                        exit 1
                    fi

                    if [ ! -f package.json ]; then
                        echo "package.json not found! Are you in the correct directory?"
                        exit 1
                    fi

                    npm install
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo "Running unit tests..."
                sh 'npm test'
            }
        }

        stage('Build Artifact') {
            steps {
                script {
                    def folderToZip = 'app'

                    if (fileExists(folderToZip)) {
                        echo "Zipping folder '${folderToZip}'..."
                        sh '''
                            if ! command -v zip &> /dev/null; then
                                echo "zip command not found! Please install zip on the agent."
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


