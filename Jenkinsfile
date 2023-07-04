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
            steps{
            withSonarQubeEnv('sonarqube') {
                 sh 'mvn clean install -f pom.xml sonar:sonar' 
                }
            }
        }
        stage('nexusupload'){
            steps{
                nexusArtifactUploader artifacts: [[artifactId: 'CounterWebApp', classifier: '', file: '/var/lib/jenkins/workspace/nexus-upload/target/CounterWebApp.war', type: 'WAR']], credentialsId: 'nexus', groupId: 'com.mkyong', nexusUrl: 'ec2-54-208-106-241.compute-1.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-shapshots', version: '1.0-SNAPSHOT'
            }
            
        }
        stage('deploy to prod'){
                steps{
                   deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://52.2.244.186:8080/')], contextPath: null, war: '**/*.war' 
                }
                
            }
        stage('slack-deploy-notification'){
            steps{
                
                slackSend channel: 'projectk', message: "App Deployment Successful, -job '${env.JOB_NAME} ${env.BUILD_NUMBER}' (${env.BUILD_URL})", teamDomain: 'projectkspacegroup', tokenCredentialId: 'slack'
            }
        }
    }
}
