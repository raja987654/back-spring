pipeline {
    agent any
    
    tools {
        maven 'maven'
    }
    
    stages {
        stage('Clean Up') {
            steps {
                deleteDir()
            }
        }
        
        stage('Clone Repo') {
            steps {
                git(
                    branch: 'main',
                    url: 'https://github.com/raja987654/back-spring'
                )
            }
        }
        
        stage('Debug Project Structure') {
            steps {
                bat 'dir /s /b'
                
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
                    if (fileExists('backend/pom.xml')) {
                        dir('backend') {
                            bat 'mvn clean install'
                            bat '"C:\\Program Files\\Docker\\Docker\\Resources\\bin\\docker.exe" build -t backend .'
                        }
                    } else if (fileExists('pom.xml')) {
                        bat 'mvn clean install'
                        bat 'mkdir docker-build-context'
                        bat 'copy target\\*.jar docker-build-context\\app.jar'
                        bat 'copy Dockerfile docker-build-context\\'
                        dir('docker-build-context') {
                            bat '"C:\\Program Files\\Docker\\Docker\\Resources\\bin\\docker.exe" build -t backend .'
                        }
                    } else {
                        error 'No pom.xml found in either root or backend directory'
                    }
                }
            }
        }
        
        stage('Run Docker Compose') {
            steps {
                script {
                    if (fileExists('backend/docker-compose.yml')) {
                        dir('backend') {
                            bat '"C:\\Program Files\\Docker\\Docker\\Resources\\bin\\docker-compose.exe" up -d'
                        }
                    } else if (fileExists('docker-compose.yml')) {
                        bat '"C:\\Program Files\\Docker\\Docker\\Resources\\bin\\docker-compose.exe" up -d'
                    } else {
                        error 'No docker-compose.yml found in either root or backend directory'
                    }
                }
            }
        }
    }
}
