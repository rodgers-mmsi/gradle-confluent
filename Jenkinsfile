pipeline {
  agent {
    kubernetes {
      defaultContainer 'gradle'
      yamlFile 'pod-template.yaml'
    }
  }
  stages {
    stage('Release') {
      when {
        branch 'master'
      }
      steps {
        // ensure we're not on a detached head
        sh "git checkout master"
        sh "git config --global credential.helper store"
        sh "jx step git credentials"

        // so we can retrieve the version in later steps
        sh "echo \$(jx-release-version) > VERSION"
        sh "jx step tag --version \$(cat VERSION)"
      }
    }
    stage('Build') {
      when {
        branch 'PR-*'
      }
      steps {
        sh "gradle clean build"
      }
    }
  }
  post {
        always {
          cleanWs()
        }
  }
}
