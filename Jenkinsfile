pipeline {
  agent any
  
  tools {
    maven "maven"
  }
  stages {
    stage('Commit change') {
      steps {
         
        echo 'Build Successful'
      }
    }

    stage('Clone source') {
      steps {
        git url: 'https://github.com/tipurmadan/DevOps-Demo-WebApp.git'
        echo 'source cloned'
      }
    }
    
    stage('Build') {
      steps {
        
         triggers {
           // poll repo every 2 minute for changes
           pollSCM('*/2 * * * *')
        }
        // get code from git repo
        git 'https://github.com/tipurmadan/DevOps-Demo-WebApp.git'
        
        
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