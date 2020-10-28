pipeline {
  agent any
  
  tools {
    maven "maven"
  }
  stages {
    stage('Commit change') {
      steps {
          triggers {
               pollSCM ''
              }
        echo 'Build Successful'
      }
    }

    stage('Build') {
      steps {
        // get code from git repo
        git 'https://https://github.com/tipurmadan/DevOps-Demo-WebApp.git'
        
        
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
