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
        // get code from git repo
        //git 'https://github.com/tipurmadan/DevOps-Demo-WebApp.git'
        sh "mvn clean compile"
        echo 'Build Success'
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deployed'
      }
    }

  }
}
