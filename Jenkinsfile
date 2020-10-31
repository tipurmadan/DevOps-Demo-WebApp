pipeline {
  agent any
  
 // triggers {
        //pollSCM 'H/2 * * * *'
   // }
  
  tools {
    maven "maven"
  }
 
  
  stages{
    
    
    stage('Build') {
      steps {
              sh 'mvn -Dmaven.test.failure.ignore=true install' 
              //sh "mvn clean compile"
              slackSend channel: "#alerts", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        
        echo 'Build Done'
      }
    }
    
    
    
    stage('SonarQube Analysis') {
	    		 environment {
       	 		scannerHome = tool 'sonarqubescanner'
    					}		
	    steps{
		    echo 'commented Sonarqube analysis'
        	//withSonarQubeEnv(credentialsId: 'sonar', installationName:'sonarqube') { 
		    //withSonarQubeEnv('sonarqube') {
       		//sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://34.121.10.65:9000// -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java -Dsonar.login=admin -Dsonar.password=admin'
		
        //}
	// timeout(time: 10, unit: 'MINUTES') {
         //   waitForQualityGate abortPipeline: true
       // }
      
  }
    }
    
    
	
	
	stage('Deploy to Test') {
		steps{
	deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: 'http://34.72.167.28:8080/')], contextPath: '/QAWebapp', war: '**/*.war'
		}
    }
	
	
	

    stage('Deploy') {
      steps {
        echo 'Deployed'
      }
    }

  }
}
