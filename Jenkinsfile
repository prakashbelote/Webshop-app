pipeline {
    agent any
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
               git 'https://github.com/prakashbelote/Webshop-app.git'        
                }
        }
        stage('CLJ Holmes Scan') {
            steps {
                sh "clj-holmes fetch-rules"
                sh "clj-holmes scan -p ./"
            }
        }
        stage('OWASP Scan') {
            steps {
                 dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        stage('Trivi File Scan') {
            steps {
                sh "trivy fs ."
            }
        }
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Webshop-Demo \
                   -Dsonar.projectKey=Webshop-Demo '''
               }
            }
        }
        stage('Docker Image Build and Deploy') {
            steps {
                sh "docker-compose up --build -d"
                }
            }
        }
        
    }
