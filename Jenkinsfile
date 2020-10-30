pipeline {
  agent any
  
  triggers {
        pollSCM 'H/2 * * * *'
    }
  
  tools {
    maven "maven"
  }
 
  
  stages{
    
    
    
      stage('SonarQube Analysis') {
        withSonarQubeEnv(credentialsId: 'sonar', installationName: 'sonarqubescanner') { // You can override the credential to be used
       		sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://34.71.72.225// -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java'
        }
	timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
	    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
	    if (qg.status != 'OK') {
	      error "Pipeline aborted due to quality gate failure: ${qg.status}"
	    }
	}             
  }
    
    
    
    stage('Build') {
      steps {
              sh 'mvn -Dmaven.test.failure.ignore=true install' 
              //sh "mvn clean compile"
               slackSend channel: "#alerts", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        
        //echo 'Build Success'
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deployed'
      }
    }

  }
}
