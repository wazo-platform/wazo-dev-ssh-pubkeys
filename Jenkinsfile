pipeline {
  agent any
  triggers {
    githubPush()
    pollSCM('H H * * *')
  }
  environment {
    MAIL_RECIPIENTS = 'dev+tests-reports@wazo.community'
  }
  options {
    skipStagesAfterUnstable()
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }
  stages {
    stage('Debian build and deploy') {
      steps {
        build job: 'build-package-no-arch', parameters: [
          string(name: 'PACKAGE', value: "${JOB_NAME}"),
        ]
      }
    }
  }
  post {
    success {
      build job: 'deploy-wazo-dev-ssh-pubkeys'
    }
    failure {
      emailext to: "${MAIL_RECIPIENTS}", subject: '${DEFAULT_SUBJECT}', body: '${DEFAULT_CONTENT}'
    }
    fixed {
      emailext to: "${MAIL_RECIPIENTS}", subject: '${DEFAULT_SUBJECT}', body: '${DEFAULT_CONTENT}'
    }
  }
}
