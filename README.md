# Jenkins Pipline To Build Maven Project


```
pipeline {
	agent any
	tools {
		//installing the requried tools before building//
		maven "MAVEN3"               
		jdk "OracleJDK8"
	}
	stages {
	    //fecthing the code fro gitlab with authecating with git credentials//
		stage('FETCHING CODE') {
			steps {
				git branch: 'feature-dna', credentialsId: '4ad397ea-a199-4701-a46b-ff30d1526735', url: 'http://gitlab.sanelahealth.com/services/labinventory.git'
			}
		}
		//building the code using maven install and archevinf the files//
		stage('BUILDING'){
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
		//Pusing the artifacts from jenkins workspace to another director//
		stage ('PUSH ARTIFACT'){
			steps {
				sh 'sudo cp /var/lib/jenkins/workspace/labinventory-be/service/target/labinventory.war /opt/sanela/server/tomcat/webapps/'
				}
			}
		//Testing the build files//	
		stage('UNIT TEST') {
			steps {
				sh 'mvn test'
			}
		}
	
	}
}

````
