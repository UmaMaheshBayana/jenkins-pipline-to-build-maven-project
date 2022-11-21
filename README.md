# Jenkins Pipline To Build Maven Project


```
pipeline {
	agent any
	tools {
		maven "MAVEN3"
		jdk "OracleJDK8"
	}
	stages {
		stage('fetch code') {
			steps {
				git branch: 'main', url: 'https://github.com/UmaMaheshBayana/testproject2.git'
			}
		}
		stage('Build'){
			steps {
				sh 'mvn clean install -DskipTest'
			}
			post {
				success {
					echo 'Now Archeving it ..'
					archiveArtifacts artifacts: '**/target/*.war'
				}
			}
			}
			stage ('PUSH ARTIFACTS'){
				steps {
					sh 'sudo cp /var/lib/jenkins/workspace/testproject2/target/*.war  /opt/sanela/server/tomcat/webapps/'
				}
			}
		
		stage('UNIT TEST') {
			steps {
				sh 'mvn test'
			}
		}
	
	}


}

````
