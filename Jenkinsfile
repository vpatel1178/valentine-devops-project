pipeline {
    agent any
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-token', url: 'https://github.com/vpatel1178/valentine-devops-project.git'
            }
        }
        stage('Trivy file system scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Valentine -Dsonar.projectName=Valentine" 
               }
            }
        }
        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker build -t vedant1178/valentine:v1 ."
                     }
                }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format json -o trivy-image-report.json vedant1178/valentine:v1" 
            }
        }
        stage('Push docker image') {
            steps {
                script {
                     withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                     sh "docker push vedant1178/valentine:v1 "
                    }
                }
            }
        }
        stage('Deploy to Container') {
            steps {
                sh "docker run -d -p 8081:80 vedant1178/valentine:v1" 
            }
        }
    }
}
