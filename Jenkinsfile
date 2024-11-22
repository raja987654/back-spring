pipeline {
    agent any
    
    tools {
        maven 'maven'  // Ensure you have 'maven' installed on Jenkins
    }
    
    stages {
        stage('Clean Up') {
            steps {
                deleteDir()  // Clean up workspace at the start
            }
        }
        
        stage('Clone Repo') {
            steps {
                git(
                    branch: 'main',  // Clone the 'main' branch of the repository
                    url: 'https://github.com/raja987654/back-spring'
                )
            }
        }
        
        stage('Debug Project Structure') {
            steps {
                // Display project structure using 'dir' command in Windows batch
                bat 'dir /s /b'
                
                // Check existence of pom.xml files in both root and backend directories
                bat '''
                    echo "Current directory:"
                    cd
                    echo "Directory content:"
                    dir
                    echo "Backend directory content (if exists):"
                    if exist backend (cd backend && dir) else (echo "Backend directory not found")
                    echo "Looking for pom.xml:"
                    if exist pom.xml (echo "pom.xml found in root") else (echo "pom.xml not found in root")
                    if exist backend\\pom.xml (echo "pom.xml found in backend") else (echo "pom.xml not found in backend")
                '''
            }
        }
        
        stage('Generate Backend Docker Image') {
            steps {
                script {
                    // Check if pom.xml exists in the 'backend' directory
                    if (fileExists('backend/pom.xml')) {
                        dir('backend') {
                            bat 'mvn clean install'  // Build backend module
                            // Ensure Dockerfile is in the correct directory for backend
                            bat 'docker build -t backend .'
                        }
                    // Otherwise, check if there is a pom.xml in the root directory
                    } else if (fileExists('pom.xml')) {
                        bat 'mvn clean install'  // Build the root project if pom.xml exists
                        // Prepare Docker build context if root pom.xml exists
                        bat 'mkdir -p docker-build-context'
                               bat 'copy target\\*.jar docker-build-context\\app.jar'
                          bat 'copy Dockerfile docker-build-context\\'
                          bat '"C:\\Program Files\\Docker\\Docker\\Resources\\bin\\docker.exe" build -t backend .
                        dir('docker-build-context') {
                            bat 'docker build -t backend .'  // Build Docker image from context
                        }
                    } else {
                        // Fail if no pom.xml is found in both locations
                        error 'No pom.xml found in either root or backend directory'
                    }
                }
            }
        }
        
        stage('Run Docker Compose') {
            steps {
                script {
                    // Check if backend/docker-compose.yml exists
                    if (fileExists('backend/docker-compose.yml')) {
                        dir('backend') {
                            bat 'docker-compose up -d'  // Run Docker Compose from backend directory
                        }
                    // Otherwise, check for docker-compose.yml in the root directory
                    } else if (fileExists('docker-compose.yml')) {
                        bat 'docker-compose up -d'  // Run Docker Compose from root directory
                    } else {
                        // Fail if no docker-compose.yml is found in both locations
                        error 'No docker-compose.yml found in either root or backend directory'
                    }
                }
            }
        }
    }
}
