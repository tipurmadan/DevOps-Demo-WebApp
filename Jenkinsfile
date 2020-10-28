pipeline {
  agent any
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
