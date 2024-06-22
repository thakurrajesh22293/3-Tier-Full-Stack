pipeline {
    agent any
    
    tools {
        nodejs 'nodejs22.3'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'main', credentialsId: 'github', url: 'https://github.com/Amritmatti/3-Tier-Full-Stack.git'
            }
        }
        
        stage('Install Package Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Unit Tests') {
            steps {
                sh "npm test"
            }
        }
        
        stage('Trivy FS Scan') {
            steps {
                sh "trivy fs --format table -o fs-report.html ."
            }
        }

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Campground -Dsonar.projectName=Campground"
                }
            }
        }
                        
        stage('Docker Build & Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t amritmatti/testrepo:latest ."
                    }
                }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o fs-report.html amritmatti/testrepo:latest"
            }
        }
        
        stage('Docker Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push amritmatti/testrepo:latest"
                    }
                }
            }
        }
    }
}
