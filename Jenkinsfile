pipeline {
    agent {
	    label 'maven-build-agent'
    }
environment {
    NEXUS_URL = 'http://52.2.21.21:8081'
	REPOSITORY = 'maven-snapshots'
	GROUP_ID = 'com.mkyong'
	ARTIFACT_ID = 'CounterWebApp'
	PACKAGING = 'war'
	USERNAME = 'admin'
	PASSWORD = 'Nexus123#'
	TOMCAT_URL = 'http://34.226.163.28:8080'
	TOMCAT_MANAGER_USER = 'deployer'
	TOMCAT_MANAGER_PASSWORD = 'Poiuytrewq321#'
	TOMCAT_CONTEXT_PATH = '/CounterWebApp'
}
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3.9.5"
    }

    stages {
        stage('Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/keyspaceits/javawebapp.git']]) 
            }
        }
        stage('Build') {
            steps {

                // Run Maven on a Unix agent.
                sh 'mvn clean install -f pom.xml'

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    slackSend channel: '#operations', message: 'Javaweb app build success', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
                
                 failure {
                    slackSend channel: '#operations', message: 'Javaweb app build has failed', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
            }
           
               
            
        }
        stage ('CodeQulity') {
            steps{
            withSonarQubeEnv('SonarQube') {
                 sh 'mvn clean install -f pom.xml sonar:sonar' 
                }
            }
        }

      stage ('NexusUpload') {
        steps {
            nexusArtifactUploader artifacts: [[artifactId: 'CounterWebApp', classifier: '', file: '/home/jenkins/workspace/javawebapp-declarative-pipeline/target/CounterWebApp.war', type: 'war']], credentialsId: 'NexusCred', groupId: 'com.mkyong', nexusUrl: '52.2.21.21:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
        }
          
      }
        stage('Deploy to Prod') {
            steps {

                deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://34.226.163.28:8080/')], contextPath: null, war: '**/*.war'
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    slackSend channel: '#operations', message: 'Javaweb app deployment success', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
                
                 failure {
                    slackSend channel: '#operations', message: 'Javaweb app deployment has failed', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
            }
           
               
            
        }
       stage('Fetch latest Artifact from Nexus') {
    steps {
	script {
	  def lastet_version= sh(script: "curl -u ${USERNAME}:${PASSWORD} -s ${NEXUS_URL}/service/rest/v1/search/assets?repository=${REPOSITORY}&group=${GROUP_ID}&name=${ARTIFACT_ID}&sort=version&format=maven2 | /usr/bin/jq -r '.items[0].version'", returnStdout: true).trim()
	  
	  sh "curl -u ${USERNAME}:${PASSWORD} -O ${NEXUS_URL}/repository/${REPOSITORY}/${GROUP_ID}/${ARTIFACT_ID}/{latest_version}/${ARTIFACT_ID}-${latest_version}.${PACKAGING}"
	  }
	}  
  }
        

    }
}
