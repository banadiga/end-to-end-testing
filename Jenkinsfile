pipeline {
  agent any

  stages {
    // first stage installs node dependencies and Cypress binary
    stage('build') {
      steps {
        // there a few default environment variables on Jenkins
        // on local Jenkins machine (assuming port 8080) see
        // http://localhost:8080/pipeline-syntax/globals#env
        echo "Running build ${env.BUILD_ID} on ${env.JENKINS_URL}"
        sh 'npm install'
      }
    }

    // this stage runs end-to-end tests, and each agent uses the workspace
    // from the previous stage
    stage('cypress parallel tests') {
      environment {
        // we will be recording test results and video on Cypress dashboard
        // to record we need to set an environment variable
        // we can load the record key variable from credentials store
        // see https://jenkins.io/doc/book/using/using-credentials/
        CYPRESS_RECORD_KEY = credentials('cypress-example-kitchensink-record-key')
        // because parallel steps share the workspace they might race to delete
        // screenshots and videos folders. Tell Cypress not to delete these folders
        CYPRESS_trashAssetsBeforeRuns = 'false'
      }

      // https://jenkins.io/doc/book/pipeline/syntax/#parallel
      parallel {
        // start several test jobs in parallel, and they all
        // will use Cypress Dashboard to load balance any found spec files
        stage('tester G1') {
          steps {
            echo "Running build ${env.BUILD_ID}"
            sh "npm run test:g1"
          }
        }

        // second tester runs the same command
        stage('tester G2') {
          steps {
            echo "Running build ${env.BUILD_ID}"
            sh "npm run test:g2"
          }
        }
      }
    }

    stage('cypress parallel group tests') {
      environment {
        CYPRESS_RECORD_KEY = credentials('cypress-example-kitchensink-record-key')
        CYPRESS_trashAssetsBeforeRuns = 'false'
      }

      parallel {
        stage('tester G3 m1') {
          steps {
            echo "Running build ${env.BUILD_ID}"
            sh "npm run test:g3"
          }
        }

        stage('tester G3 m2') {
          steps {
            echo "Running build ${env.BUILD_ID}"
            sh "npm run test:g3"
          }
        }
      }
    }
  }
}