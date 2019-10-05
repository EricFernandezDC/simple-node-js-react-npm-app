pipeline {
    agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000'
        }
    }
    environment { 
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
                input message: 'Finished using the web site? (Click "Proceed" to continue)' 
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
     post {
    success {
      script {
        currentBuild.result = "SUCCESS"

        /* Custom data map for InfluxDB */
        def custom = [:]
        custom['branch']      = "master"
        custom['environment'] = "prod"
        custom['part']        = 'jenkins'
        custom['version']     = "1.0"

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'devops-kpi'])
      }


    }

    failure {
      script {
        currentBuild.result = "FAILURE"

        /* Custom data map for InfluxDB */
        def custom = [:]
        custom['branch']      = "master"
        custom['environment'] = "prod"
        custom['part']        = 'jenkins'
        custom['version']     = "1.0"

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'devops-kpi'])
      }


    }

    unstable {
      script {
        currentBuild.result = "FAILURE"

        /* Custom data map for InfluxDB */
        def custom = [:]
        custom['branch']      = "master"
        custom['environment'] = "prod"
        custom['part']        = 'jenkins'
        custom['version']     = "1.0"

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'devops-kpi'])
      }


    }

    aborted {
      script {
        currentBuild.result = "FAILURE"

        /* Custom data map for InfluxDB */
        def custom = [:]
        custom['branch']      = "master"
        custom['environment'] = "prod"
        custom['part']        = 'jenkins'
        custom['version']     = "1.0"

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'devops-kpi'])
      }


    }

  }
}