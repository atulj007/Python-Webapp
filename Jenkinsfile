pipeline {
    agent any
    
     tools {
        jdk 'jdk17'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }
   

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/atulj007/Python-Webapp.git'
            }
        }
        
        stage('OWASP Dependency Checks') {
            steps {
               dependencyCheck additionalArguments: ' --scan ./', odcInstallation:'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                   sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Python-Webapp \
                -Dsonar.projectKey=Python-Webapp'''
                }
            }
        }
        
        stage('Docker Build & Tagg') {
            steps {
                    script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "make image"
                        
                    }
                  }
                }
            }
        
       stage('Docker Push Image') {
            steps {
                    script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "make push"
                        
                    }
                  }
                }
            }
            
            stage('Deploy to Docker Container') {
            steps {
                    script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker run -d  -p 5000:5000 atulj79/python-webapp:latest"
                        
                    }
                  }
                }
            }
    }
  }  
