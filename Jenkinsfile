pipeline {
  agent any

  tools {
    nodejs 'node 16'
  }

  options {
    buildDiscarder logRotator(
        artifactDaysToKeepStr: '',
        artifactNumToKeepStr: '',
        daysToKeepStr: '1',
        numToKeepStr: '5'
    )
  }

  stages {

    stage('Policy') {
      steps {
        script {
          nexusPolicyEvaluation(
              enableDebugLogging: false,
              iqStage: 'build',
              iqApplication: 'sandbox-application',
              failBuildOnNetworkError: true,
              iqScanPatterns: [
                  [scanPattern: 'package.json'],
                  [scanPattern: 'package-lock.json']
              ],
              callflow: [
                  logLevel: 'DEBUG',
                  jsAnalysis: [
                      enable: true,
                      algorithm: 'NBA',
                      sourceFiles: [
                          [pattern: 'src/**/*.js']
                      ]
                  ]
              ]
          )

          archiveArtifacts(
              artifacts: '**/bomxray.log',
              followSymlinks: false
          )
        }
      }
    }
  }
}