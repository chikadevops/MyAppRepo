
pipeline {
    agent any
    
    tools {
        maven 'Maven3'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '0cac6422-9a35-4a1a-aaca-e05f605e7eda', url: 'https://github.com/chikadevops/MyAppRepo/']])
            }
        }
    
        stage('Build') {
            steps {
                sh "mvn clean install -f MyWebApp/pom.xml"
            }
        }
        
        stage ('JaCoCo') {
            steps {
                jacoco()
            }
        }
        
        stage('Code Quality') {
            steps {
                withSonarQubeEnv("SonarQube"){
                sh "mvn sonar:sonar -f MyWebApp/pom.xml"
                }
            }
        }
        
        