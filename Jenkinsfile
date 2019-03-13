pipeline {
  agent any
  stages {
    stage('Build') {
    agent {label 'master'}
      steps {
        bat 'echo "Build"'
      }
    }
  }
}
