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
	environment {
			EMAIL_TO = 'numbayana@sanelahealth.com,sunnam@sanelahealth.com'
			BUILD_URL='http://dev.sanelahealth.com:8080/job/async-be'
			PROJECT_NAME='async-be'
			
    		     }
		post {
        
			success {
				mail body: "Check console output at ${env.BUILD_URL} to view the results."
                    		to: "${EMAIL_TO}", 
                    		subject: "Build Success in Jenkins: ${env.PROJECT_NAME}"
        			}
        
			failure {
				mail body: 'Check console output at $BUILD_URL to view the results. 
                    		to: "${EMAIL_TO}", 
                    		subject: 'Build failed in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        			}
			unstable {
				mail body: 'Check console output at $BUILD_URL to view the results. 
                    		to: "${EMAIL_TO}", 
                    		subject: 'Unstable build in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        			}
			changed {
				mail body: 'Check console output at $BUILD_URL to view the results.', 
                    		to: "${EMAIL_TO}", 
                    		subject: 'Jenkins build is back to normal: $PROJECT_NAME - #$BUILD_NUMBER'
        			}
    			}
}

````
