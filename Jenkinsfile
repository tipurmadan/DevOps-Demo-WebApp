pipeline {
  agent any
  
  triggers {
        pollSCM 'H/2 * * * *'
    }
  
  tools {
    maven "maven"
  }
 
  
  stages{
    
    
    stage('Build') {
      steps {
              //sh 'mvn -Dmaven.test.failure.ignore=true install' 
              //sh "mvn clean compile"
               //slackSend channel: "#alerts", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        
        echo 'Build Success'
      }
    }
    
    
    
    stage('SonarQube Analysis') {
	     environment {
        scannerHome = tool 'sonarqubescanner'
    }
	    steps{
        withSonarQubeEnv(credentialsId: 'sonar-test', installationName:'sonarqube') { 
       		sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://34.71.72.225// -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java -Dsonar.login=admin -Dsonar.password=admin'
        }
	 timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
      
  }
    }
    
    

    stage('Deploy') {
      steps {
        echo 'Deployed'
      }
    }

  }
}
