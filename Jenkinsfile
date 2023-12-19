pipeline {
    agent any
    tools{
        maven 'Maven3.9.5'
    }
    stages {
        stage("checkout") {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/test']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/keyspaceits/javawebapp.git']]]) 
            }
        }
        stage("build") {
            steps {
                sh 'mvn clean install -f pom.xml'
            }
        }
        stage("CodeQuality") {
            steps {
                withSonarQubeEnv('SonarQube') {
                sh 'mvn sonar:sonar -f pom.xml'
                }
            }
        }
        stage("dev deploy") {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://34.226.163.28:8080/')], contextPath: null, war: '**/*.war'
            }
        }
		stage('Dev apprl for QA') {
            steps {
                echo "taking approval from Dev Manager"
                timeout(time: 7, unit: 'DAYS') {
                    input message: 'Do you want to proceed to QA?', submitter:'admin'
                }
            }
        }
        stage('QA Deploy'){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://34.226.163.28:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        
    }
}
