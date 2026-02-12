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

    stage('Coverity via Jenkins Plugin') {
      steps {
        // Plugin provides steps that bind Coverity tools/config into env
        // (Exact step names depend on plugin + tool configuration in Jenkins Global Tool Config)
        withCoverityEnv(coverityToolName: 'coverity-2023', connectInstance: 'coverity-connect') {
          sh '''
            set -euo pipefail
            cov-build --dir idir npm ci && npm run build
            cov-analyze --dir idir --all
            cov-commit-defects --dir idir --stream juice-shop-main
          '''
        }
      }
    }
  }
}

  environment {
    BLACKDUCK_URL = credentials('blackduck-url')
    BLACKDUCK_API_TOKEN = credentials('blackduck-api-token')
  }
