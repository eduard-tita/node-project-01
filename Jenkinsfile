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
                  [scanPattern: 'h2-1.4.196.jar'],
                  [scanPattern: 'package.json'],
                  [scanPattern: 'package-lock.json']
              ],
              reachability: [
                  logLevel: 'DEBUG',
                  javaAnalysis: [
                      enable: true,
                      entrypointStrategy: 'JAVA_MAIN',
                      namespaces: [
                          [namespace: 'org.h2.tools'],
                          [namespace: 'org.h2.server'],
                          [namespace: 'org.h2.util']
                      ]
                  ],
                  jsAnalysis: [
                      enable: true,
                      force: true,
                      //algorithm: 'NBA',
                      //projectDirectory: '.',                // relative to workspace
                      packageJsonFile: 'package.json',      // relative to workspace
                      sourceFiles: [                        // relative to workspace
                          [pattern: 'src/**/*']
                      ],
                      excludeFiles: [                      // relative to workspace
                          [pattern: 'blah/**/*']
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