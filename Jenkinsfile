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
        
        stage('Nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: 'ebc54a45-78b0-410b-b693-c275274850cd', groupId: 'com.dept.app', nexusUrl: 'ec2-35-153-57-193.compute-1.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
            }
        }
        
        stage('DEV Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: '3bde9024-1c06-4478-9524-4a3c8f621e86', path: '', url: 'http://ec2-54-159-156-84.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*war'
            }
        }
        
        stage('Slack notify DEV') {
            steps {
                slackSend channel: 'devops-tutorials', message: 'Successfully deployed to DEV Environment'
            }
        }
        
        stage('DEV Approve') {
            steps {
                echo "taking approval from DEV Manager for QA Deployment"
                timeout(time: 7, unit: 'DAYS') {
                    input message: "Do you approve deployment to QA environment?", submitter:'admin'
                }
            }
        }
        
        stage('QA notify') {
            steps {
                slackSend channel: 'devops-tutorials', message: 'Successfully deployed to QA environment... Kindly access project immediately'
            }
        }
        
        stage('QA Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: '3bde9024-1c06-4478-9524-4a3c8f621e86', path: '', url: 'http://ec2-54-159-156-84.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*war'
            }
        }
        
        stage('QA Apporve') {
            steps {
                echo "Taking approval from QA Manager for PROD Manager"
                timeout(time: 4, unit: 'DAYS') {
                    input message: "Do you approve deployment to PROD environment?"
                }
            }
        }
        
         stage('PROD Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: '3bde9024-1c06-4478-9524-4a3c8f621e86', path: '', url: 'http://ec2-54-159-156-84.compute-1.amazonaws.com:8080/')], contextPath: null, war: '**/*war'
            }
        }
        
        stage('PROD notify') {
            steps {
                slackSend channel: 'devops-tutorials', message: 'Successfully deployed to PROD environment'
            }
        }
    } 
}
