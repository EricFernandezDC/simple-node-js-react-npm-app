pipeline {
  agent {
    docker {
      image 'node:6-alpine'
      args '-p 3000:3000'
    }

  }
  stages {
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test') {
      steps {
        sh './simple-node-js-react-npm-app/jenkins/scripts/test.sh'
      }
    }
    stage('Deliver') {
      steps {
        sh './simple-node-js-react-npm-app/jenkins/scripts/deliver.sh'
        input 'Finished using the web site? (Click "Proceed" to continue)'
        sh './simple-node-js-react-npm-app/jenkins/scripts/kill.sh'
      }
    }
  }
  environment {
    CI = 'true'
  }
}