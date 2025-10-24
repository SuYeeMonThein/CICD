pipeline {
  agent { label 'linux' }
  options {
    ansiColor('xterm')
    skipDefaultCheckout(false)
    timestamps()
  }
  environment {
    TIDY_OPTS = '-q -e'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Validate HTML') {
      steps {
        sh 'test -f index.html'
        sh '''
          if command -v tidy >/dev/null 2>&1; then
            tidy ${TIDY_OPTS} index.html
          else
            echo 'tidy not installed; skipping lint'
          fi
        '''
      }
    }
    stage('Archive Artifact') {
      steps {
        archiveArtifacts artifacts: 'index.html', fingerprint: true
      }
    }
    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        sh '''
          mkdir -p output
          cp index.html output/
          # TODO: replace with real deployment command, e.g. aws s3 sync output s3://your-bucket/
        '''
      }
    }
  }
  post {
    failure {
      mail to: 'you@example.com', subject: "Static site build failed", body: "Build ${env.BUILD_URL}"
    }
  }
}
