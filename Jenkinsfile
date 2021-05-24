pipeline {

  options {
    timeout(time:20, unit: 'MINUTES')
  }
  
  agent {
    node {
      label '1604-slave'
    }
  }
  
  stages {
    stage('Prepare') {
      steps {
        echo 'Testing ..'
      }
    }
  }
}
