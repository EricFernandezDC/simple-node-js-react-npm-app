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
        sh '''#!/usr/bin/env sh

            echo \'The following "npm" command (if executed) installs the "cross-env"\'
            echo \'dependency into the local "node_modules" directory, which will ultimately\'
            echo \'be stored in the Jenkins home directory. As described in\'
            echo \'https://docs.npmjs.com/cli/install, the "--save-dev" flag causes the\'
            echo \'"cross-env" dependency to be installed as "devDependencies". For the\'
            echo \'purposes of this tutorial, this flag is not important. However, when\'
            echo \'installing this dependency, it would typically be done so using this\'
            echo \'flag. For a comprehensive explanation about "devDependencies", see\'
            echo \'https://stackoverflow.com/questions/18875674/whats-the-difference-between-dependencies-devdependencies-and-peerdependencies.\'
            set -x
            # npm install --save-dev cross-env
            set +x

            echo \'The following "npm" command tests that your simple Node.js/React\'
            echo \'application renders satisfactorily. This command actually invokes the test\'
            echo \'runner Jest (https://facebook.github.io/jest/).\'
            set -x
            npm test'''
      }
    }
    stage('Deliver') {
      steps {
        sh '''#!/usr/bin/env sh

        echo \'The following "npm" command builds your Node.js/React application for\'
        echo \'production in the local "build" directory (i.e. within the\'
        echo \'"/var/jenkins_home/workspace/simple-node-js-react-app" directory),\'
        echo \'correctly bundles React in production mode and optimizes the build for\'
        echo \'the best performance.\'
        set -x
        npm run build
        set +x

        echo \'The following "npm" command runs your Node.js/React application in\'
        echo \'development mode and makes the application available for web browsing.\'
        echo \'The "npm start" command has a trailing ampersand so that the command runs\'
        echo \'as a background process (i.e. asynchronously). Otherwise, this command\'
        echo \'can pause running builds of CI/CD applications indefinitely. "npm start"\'
        echo \'is followed by another command that retrieves the process ID (PID) value\'
        echo \'of the previously run process (i.e. "npm start") and writes this value to\'
        echo \'the file ".pidfile".\'
        set -x
        npm start &
        sleep 1
        echo $! > .pidfile
        set +x

        echo \'Now...\'
        echo \'Visit http://localhost:3000 to see your Node.js/React application in action.\'
        echo \'(This is why you specified the "args \'\'-p 3000:3000\'\'" parameter when you\'
        echo \'created your initial Pipeline as a Jenkinsfile.)\'
        '''
        input 'Finished using the web site? (Click "Proceed" to continue)'
        sh '''#!/usr/bin/env sh

        echo \'The following command terminates the "npm start" process using its PID\'
        echo \'(written to ".pidfile"), all of which were conducted when "deliver.sh"\'
        echo \'was executed.\'
        set -x
        kill $(cat .pidfile)'''
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

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'jenkins'])
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

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'jenkins'])
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

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'jenkins'])
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

        step([$class: 'InfluxDbPublisher', customData: custom, target: 'jenkins'])
      }
    }
  }
  environment {
    CI = 'true'
  }
}