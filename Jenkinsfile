pipeline {
  agent any
  
  triggers {
        pollSCM 'H/2 * * * *'
    }
  
  tools {
    maven "maven"
  }
 
  
  stages{
    
    
    
     stage('Sonarqube') {
    environment {
        scannerHome = tool 'sonarqubescanner'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
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
