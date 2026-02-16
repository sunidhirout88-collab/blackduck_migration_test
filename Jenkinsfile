pipeline {
  agent any

  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Download Bridge CLI') {
      steps {
        sh '''
          set -euo pipefail
          mkdir -p .ci-tools
          # TODO: Replace with your actual download (curl/wget/artifactory)
          # Example:
          #   curl -sSL -o .ci-tools/bridge "<YOUR_BRIDGE_CLI_URL>"
          #   chmod +x .ci-tools/bridge
          # Placeholder:
          if [ ! -x .ci-tools/bridge ]; then
            printf '#!/usr/bin/env bash
echo "Bridge CLI placeholder. Replace with real download."
' > .ci-tools/bridge
            chmod +x .ci-tools/bridge
          fi
        '''
      }
    }
    stage('Black Duck via Bridge CLI') {
      steps {
        sh '''
          set -euo pipefail
          test -x .ci-tools/bridge
          # Ensure bridge.yml has a "blackduck" stage configured
          .ci-tools/bridge --stage blackduck --input bridge.yml
        '''
      }
    }
   }
 }

  environment {
    BLACKDUCK_URL = credentials('blackduck-url')
    BLACKDUCK_API_TOKEN = credentials('blackduck-api-token')
  }
