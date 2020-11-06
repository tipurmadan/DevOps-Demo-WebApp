def JENKINS_URL = "http://35.239.65.77:8080/"

pipeline {
  agent any

// environment variables
  environment {
    buildnum = currentBuild.getNumber()
    
    jiraIssue = 'SQUAD3-2'
    branchName = 'SQUAD3-2'
    
    gitURL = "https://github.com/tipurmadan/DevOps-Demo-WebApp.git"
    gitBranch = "*/master"
    
    slackChannel = "#alerts"
    slackChannelfinal = "#devops-learning"
    
    JiraSitename = 'squad-3-devops.atlassian.net'
    
    testUrl = 'http://35.225.186.14:8080/'
    ProdUrl =  'http://35.226.34.158:8080/'
    }
    
    
  tools {
    maven "maven"
  }	
  
  stages{
  
// 1.  	
	  stage('Checkout') {
		  steps	  {
			  checkout([$class: 'GitSCM', branches: [[name: gitBranch]], userRemoteConfigs: [[url: gitURL]]])
	                  jiraSendBuildInfo branch: "${branchName}", site: JiraSitename
		  }
  	  }
	  
//2. 	  
	  
   	 stage('Build') {
     		 steps {
              		sh 'mvn -Dmaven.test.failure.ignore=true install' 
		      //sh "mvn clean install"
		      slackSend channel: slackChannel, message: "Build Started:" + JENKINS_URL + "job/" + env.JOB_NAME+"/"+ env.BUILD_NUMBER
		      jiraSendBuildInfo branch: 'master', site: JiraSitename
        
        	      echo 'Build Done' 
      	}
	
   	}
    
 //3   
    
  //  	stage('Static code Analysis') {
//	    		 environment {
 //      	 			scannerHome = tool 'sonarqubescanner'
  // 				}		
//	    steps{
//		  	 withSonarQubeEnv('sonarqube') {
  //    		 	sh 'mvn clean package sonar:sonar -Dsonar.sources=. -Dsonar.tests=. -Dsonar.test.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.exclusions=**/test/java/servlet/createpage_junit.java -Dsonar.login=admin -Dsonar.password=admin'
//			slackSend channel: slackChannel, message: "SonarQube Analysis Done successfully"
//		        }
 // 		}
  //  }
    
  //4  
	stage('Deploy to Test') {
		steps{
			deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: testUrl)], contextPath: '/QAWebapp', war: '**/*.war'
			slackSend channel: slackChannel, message: "Deployed to Test server"
			jiraSendDeploymentInfo environmentId: 'Test', environmentName: 'Test Env', environmentType: 'development', site: JiraSitename, state: 'successful'
			jiraAddComment comment: 'Deployed to Test ', idOrKey: "${jiraIssue}", site: 'jirasite'
		}
	    }
	 
 //5	  
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

 //6	  
	   stage('UI Test') {
		   steps{
			    	sh 'mvn test -f functionaltest/pom.xml'
				publishHTML([escapeUnderscores:true,allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\functionaltest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'UI_TEST_Report', reportTitles: 'HTML Report'])
			   	slackSend channel: slackChannel, message: "UI Test report published"
		   }
    			}
	  
//7	  
	   //stage('Performance Test') {
	//	 steps{
	//		echo 'BlazeMeterTest' 
	//		blazeMeterTest credentialsId: 'blazemeter', testId: '8487271.taurus', workspaceId: '646655'
	//		 slackSend channel: slackChannel, message: "Performance test report published"
	//	   }
 //  }
	  
	  
//8	  
	  stage('Deploy to Prod') {
		  steps{
	      deploy adapters: [tomcat8(credentialsId: 'tomcat', path: '', url: ProdUrl)], contextPath: '/ProdWebapp', war: '**/*.war'
			  slackSend channel: slackChannel, message: "Deployed to prod"
			  jiraSendDeploymentInfo environmentId: 'Prod', environmentName: 'Production', environmentType: 'Production', site: JiraSitename , state: 'successful'
			  	jiraAddComment comment: 'Deployed to prod', idOrKey: "${jiraIssue}", site: 'jirasite'
        			
		  }
		  
         }
	  
//9	  
	  stage('Sanity Test') {
		  steps{
			   sh 'mvn test -f Acceptancetest/pom.xml'
				publishHTML([escapeUnderscores:true,allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '\\Acceptancetest\\target\\surefire-reports', reportFiles: 'index.html', reportName: 'Sanity Test Report', reportTitles: 'HTML Report'])
			  slackSend channel: slackChannel, message: "Sanity Test report published"
        			jiraTransitionIssue idOrKey: "${jiraIssue}", input: [transition: [id: '31']] , site: 'jirasite'
		  }
    }
	
	
	    

  }
	post { 
		failure {
			slackSend channel: slackChannelfinal, color:'RED', message: "Pipeline FAILURE ${env.JOB_NAME} #${env.BUILD_NUMBER}"
		}
		success {
			slackSend channel: slackChannelfinal, color:'good', message: "Pipeline Completed ${currentBuild.fullDisplayName} successfully"
		}
	}
	
	
		
}
