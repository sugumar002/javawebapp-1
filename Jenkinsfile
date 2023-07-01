pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3.9.3"
    }

    stages {
        stage('checkout'){
            steps {
            // Get some code from a GitHub repository
             git 'https://github.com/keyspaceits/javawebapp.git'
            }
        }
        stage('Build') {
            steps {
                
               // Run Maven on a Unix agent.
                sh "mvn clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('slack-build-notification'){
            steps{
                
                slackSend channel: 'projectk', message: 'App Build Successful', teamDomain: 'projectkspacegroup', tokenCredentialId: 'slack'
            }
        }
        stage ('CodeQulity') {
            withSonarQubeEnv('sonarqube') {
                 sh 'mvn clean install -f pom.xml sonar:sonar' 
                }
            }
        stage('deploy to prod'){
                steps{
                   deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://54.242.100.220:8080/')], contextPath: null, war: '**/*.war' 
                }
                
            }
        stage('slack-deploy-notification'){
            steps{
                
                slackSend channel: 'projectk', message: "App Deployment Successful, -job '${env.JOB_NAME} ${env.BUILD_NUMBER}' (${env.BUILD_URL})", teamDomain: 'projectkspacegroup', tokenCredentialId: 'slack'
            }
        }
    }
}
