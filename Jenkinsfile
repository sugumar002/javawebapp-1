pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3.9"
    }

    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/keyspaceits/javawebapp.git']])
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean install -f pom.xml'
            }
        
        }
        stage('build-notify') {
            steps {
                slackSend channel: 'opsteam', message: 'build success', teamDomain: 'creativeworks-corp', tokenCredentialId: 'slack'
            }
        }
        stage('CodeQuality') {
            steps {
            withSonarQubeEnv('SonarQube') {
            sh 'mvn clean install -f pom.xml sonar:sonar'
            }
            }
        }
        stage('Prod Deploy') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://34.234.40.136:8080')], contextPath: null, war: '**/*.war'
            }
        }
        stage('Deploy-notify') {
            steps {
                slackSend channel: 'opsteam', message: 'Deployment Success', teamDomain: 'creativeworks-corp', tokenCredentialId: 'slack'
            }
        }
    }
}
