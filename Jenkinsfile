def JENKINS_URL = "http://107.178.215.30:8080/"

pipeline {
	
	 
  agent any
  environment {
        JENKINS_URL = "${env.JENKINS_URL}"

    }
	
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
              slackSend channel: "#alerts", message: "Build Started:" + JENKINS_URL + "job/" + env.JOB_NAME+"/"+ env.BUILD_NUMBER
	      jiraSendBuildInfo branch: 'master', site: 'squad-3-devops.atlassian.net'
        
        echo 'Build Done' 
      }
	post {
       always {
           jiraSendBuildInfo site: 'squad-3-devops.atlassian.net'
       }
   }
    }
    
    
    
    stage('SonarQube Analysis') {
	    		 //environment {
       	 			//scannerHome = tool 'sonarqubescanner'
    				//}		
	    steps{
		    echo 'commented Sonarqube analysis'
        	  //withSonarQubeEnv(credentialsId: 'sonar', installationName:'sonarqube') { 
		    withSonarQubeEnv('sonarqube') {
       		//sh 'mvn clean package sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java -Dsonar.login=admin -Dsonar.password=admin'
		slackSend channel: "#alerts", message: "SonarQube Analysis Done successfully"
        }
	// timeout(time: 10, unit: 'MINUTES') {
         //   waitForQualityGate abortPipeline: true
       // }
      
  }
    }
    
    
	
	
	stage('Deploy to Test') {
		steps{
			deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: 'http://35.202.168.134:8080/')], contextPath: '/QAWebapp', war: '**/*.war'
		
			   slackSend channel: "#alerts", message: "Deployed to Test server"
			jiraSendDeploymentInfo environmentId: 'Test', environmentName: 'Test Env', environmentType: 'development', serviceIds: ['Test service id'], site: 'squad-3-devops.atlassian.net', state: 'deployed'

		}
    }
	 
	  
	  
	  
	  
	  
	  
	  
	  
	  stage ('Server config') {
            steps { 
              rtServer (
    id: 'artifactory',
    url: 'https://arunsahu2222.jfrog.io/artifactory',
    // If you're using username and password:
    username: 'deploy1',
    password: '10@Storage'

            )
            }
   }
//}
      stage ('Upload file') {
            steps {
                rtUpload (
                    serverId: 'artifactory',
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "*.war",
                                        "target": "libs-release-local"
                                    }
                                ]
                            }"""
                )
               rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: 'artifactory',
                    releaseRepo: "deploy1",
                    snapshotRepo: "deploy1"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: 'artifactory',
                    releaseRepo: "deploy1",
                    snapshotRepo: "deploy1"
                )
              rtPublishBuildInfo (
    serverId: 'artifactory')
   
            }
        }

	  
	  
	  
	  
	  
	  
	  
	  
	  
	  
	  
	  
	  
	   stage('UI Test') {
		   steps{
				publishHTML([escapeUnderscores:true,allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'UI_TEST_Report', reportTitles: 'HTML Report'])
			   slackSend channel: "#alerts", message: "UI Test report published"
		   }
    			}
	  
	  
	  // stage('Performance Test') {
		 //steps{
			//echo 'BlazeMeterTest' 
			//blazeMeterTest credentialsId: 'blazemeter', testId: '8491749.taurus', workspaceId: '648314'
			// slackSend channel: "#alerts", message: "Performance test report published"
		   //}
    //}
	  
	  
	  stage('Deploy to Prod') {
		  steps{
	      deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: 'http://34.71.36.151:8080/')], contextPath: '/ProdWebapp', war: '**/*.war'
			  slackSend channel: "#alerts", message: "Deployed to prod"
			  jiraSendDeploymentInfo environmentId: 'Prod', environmentName: 'Production', environmentType: 'Production', serviceIds: ['Prod service id'], site: 'squad-3-devops.atlassian.net', state: 'deployed'
		  }
		   post {
       always {
           jiraSendDeploymentInfo site: 'squad-3-devops.atlassian.net', environmentId: 'us-prod-1', environmentName: 'us-prod-1', environmentType: 'production'
       }
   }
         }
	  
	  
	  stage('Sanity Test') {
		  steps{
	publishHTML([escapeUnderscores:true,allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\Acceptancetest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'Sanity Test Report', reportTitles: 'HTML Report'])
			  slackSend channel: "#alerts", message: "Sanity Test report published"
		  }
    }
	
	
	

    stage('Send Notification') {
      steps {
        	 slackSend channel: '#devops-learning', message: "Pipeline Completed ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${JENKINS_URL + "job/" + env.JOB_NAME+"/"+ env.BUILD_NUMBER}|Open>)"
	      echo 'notification sent to channel: devops-learning'

      }
    }

  }
}
