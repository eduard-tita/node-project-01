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

    stage('Build') {
      steps {
        script {
          sh 'node -v'
          sh 'npm install'
        }
      }
    }

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
                      force: true,
                      algorithm: 'NBA',
                      projectDirectory: '.',                // relative to workspace
                      packageJsonFile: 'package.json',      // relative to workspace
                      sourceFiles: [                        // relative to workspace
                          [pattern: 'src/**/*.js']
                      ],
                      excludeFiles: [                      // relative to workspace
                          [pattern: 'blah/**/*.js']
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