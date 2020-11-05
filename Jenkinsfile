def JENKINS_URL = "http://35.223.82.59:8080/"

pipeline {
  agent any
	
  tools {
    maven "maven"
  }
  
  stages{
	  

	  
	  stage('Checkout') {
		  steps	  {
        		//git url: 'https://github.com/tipurmadan/DevOps-Demo-WebApp.git'
			  checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[url: 'https://github.com/tipurmadan/DevOps-Demo-WebApp.git']]])
			  	
			        // jiraAddComment comment: 'Checkout Test ', idOrKey: "${'SQUAD3-2'}", site: 'squad-3-devops.atlassian.net'
        			//jiraTransitionIssue idOrKey: "${'SQUAD3-2'}", input: [transition: [id: '21']] , site: 'squad-3-devops.atlassian.net'
			      jiraSendBuildInfo branch: "${'Devops-Bootcamp-2020'}", site: 'squad-3-devops.atlassian.net'
		  }
    }
	  
	  
    stage('Build') {
      steps {
              //sh 'mvn -Dmaven.test.failure.ignore=true install' 
              sh "mvn clean install"
              slackSend channel: "#alerts", message: "Build Started:" + JENKINS_URL + "job/" + env.JOB_NAME+"/"+ env.BUILD_NUMBER
	      jiraSendBuildInfo branch: 'master', site: 'squad-3-devops.atlassian.net'
        
        echo 'Build Done' 
      }
	
   }
    
    
    
    stage('Static code Analysis') {
	    		 environment {
       	 			scannerHome = tool 'sonarqubescanner'
   				}		
	    steps{
		   withSonarQubeEnv('sonarqube') {
      	sh 'mvn clean package sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java -Dsonar.login=admin -Dsonar.password=admin'
		slackSend channel: "#alerts", message: "SonarQube Analysis Done successfully"
        }
	
      
  }
    }
    
    
	
	
	stage('Deploy to Test') {
		steps{
			deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: 'http://104.198.208.88:8080/')], contextPath: '/QAWebapp', war: '**/*.war'
		
			   slackSend channel: "#alerts", message: "Deployed to Test server"
			jiraSendDeploymentInfo environmentId: 'Test', environmentName: 'Test Env', environmentType: 'development', serviceIds: ['http://104.198.208.88:8080/QAwebapp'], site: 'squad-3-devops.atlassian.net', state: 'successful'
			         jiraAddComment comment: 'Deployed to Test ', idOrKey: "${'SQUAD3-2'}", site: 'jirasite'
        			
		}
    }
	 
	  
	  
	
	  
	  
	  
	  
	  
	  
//}
      stage ('Deploy Artifacts') {
            steps {
		    
	    
                rtUpload (
                    serverId: 'artifactory',
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "**/*.war",
                                        "target": "libs-release-local"
                                    }
                                ]
                            }"""
                )
        
              rtPublishBuildInfo (
    serverId: 'artifactory')
   
            }
        }

	  
	  
	  
	   stage('UI Test') {
		   steps{
			    	sh 'mvn test -f functionaltest/pom.xml'
				publishHTML([escapeUnderscores:true,allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'UI_TEST_Report', reportTitles: 'HTML Report'])
			   slackSend channel: "#alerts", message: "UI Test report published"
		   }
    			}
	  
	  
	   //stage('Performance Test') {
	//	 steps{
	//		echo 'BlazeMeterTest' 
	//		blazeMeterTest credentialsId: 'blazemeter', testId: '8487271.taurus', workspaceId: '646655'
	//		 slackSend channel: "#alerts", message: "Performance test report published"
	//	   }
 //  }
	  
	  
	  stage('Deploy to Prod') {
		  steps{
	      deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: 'http://35.238.187.100:8080/')], contextPath: '/ProdWebapp', war: '**/*.war'
			  slackSend channel: "#alerts", message: "Deployed to prod"
			  jiraSendDeploymentInfo environmentId: 'Prod', environmentName: 'Production', environmentType: 'Production', serviceIds: ['http://35.238.187.100:8080/ProdWebapp'], site: 'squad-3-devops.atlassian.net', state: 'successful'
			  	jiraAddComment comment: 'Deployed to prod', idOrKey: "${'SQUAD3-2'}", site: 'jirasite'
        			
		  }
		  
         }
	  
	  
	  stage('Sanity Test') {
		  steps{
			   sh 'mvn test -f Acceptancetest/pom.xml'
	publishHTML([escapeUnderscores:true,allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\Acceptancetest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'Sanity Test Report', reportTitles: 'HTML Report'])
			  slackSend channel: "#alerts", message: "Sanity Test report published"
        			jiraTransitionIssue idOrKey: "${'SQUAD3-2'}", input: [transition: [id: '31']] , site: 'jirasite'
		  }
    }
	
	
	    

  }
	post { 
		failure {
			slackSend channel: '#devops-learning', color:'RED', message: "Pipeline FAILURE ${env.JOB_NAME} #${env.BUILD_NUMBER}"
		}
		success {
			slackSend channel: '#devops-learning', color:'good', message: "Pipeline Completed ${currentBuild.fullDisplayName} successfully"
		}
	}
	
	
		
}
