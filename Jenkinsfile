pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3.9.8"
    }

    stages {
	    stage('checkout'){
		 steps{
		 checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/keyspaceits/javawebapp.git']])
		 }
		}
		
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
	    stage('upload artfacts tonecus'){
		    steps{
                      nexusArtifactUploader artifacts: [[artifactId: 'CounterWebApp', classifier: '', file: '/var/lib/jenkins/workspace/scripted-pipeline/target/CounterWebApp.war', type: 'WAR']], credentialsId: 'nexus', groupId: 'com.mkyong', nexusUrl: '172.31.9.61:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
                  }
	    }
           stage('deploy to prod') {
                steps {
                   deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://172.31.91.56:8080/')], contextPath: null, war: '**/*.war'
                }
        }
    }
}
