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
        
        stage('Generate Backend Docker Image') {
            steps {
                dir('backend') {
                    // Using bat instead of sh for Windows
                    bat 'mvn clean install'
                    bat 'docker build -t backend .'
                }
            }
        }
        
        stage('Run Docker Compose') {
            steps {
                dir('backend') {
                    // Using bat instead of sh for Windows
                    bat 'docker-compose up -d'
                }
            }
        }
    }
    
    post {
        failure {
            echo 'Pipeline failed! Check the logs for details.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}
