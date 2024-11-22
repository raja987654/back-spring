pipeline {
    agent any
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
                git branch: 'main',
                    url: 'https://github.com/raja987654/back-spring'
            }
        }
        
        stage('Generate Backend Docker Image') {
            steps {
                dir('backend') {
                    sh 'mvn clean install'
                    sh 'docker build -t backend .'
                }
            }
        }
        
        stage('Run Docker Compose') {
            steps {
                dir('backend') {
                    sh 'docker-compose up -d'
                }
            }
        }
    }
}pose') {
            steps {
                dir('backend') {
                    sh 'docker-compose up -d'
                }
            }
        }
    }
}
