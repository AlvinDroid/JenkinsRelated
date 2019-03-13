pipeline {
  agent none
  stages {
    stage('Build') {
      steps {
        retry(count: 3) {
          bat 'xcopy "\\\\10.59.60.216\\Build_Storage\\builds_cn\\SBO\\B1ANY\\B1OD_1.1_DEV\\2019-03-13_09-49-55_1613302\\B1OD" "C:\\git\\CloudJenkins\\Build" /s  /e  /y /i'
        }

      }
    }
    stage('Test') {
      agent {
        node {
          label 'master'
        }

      }
      steps {
        bat 'echo test'
      }
    }
  }
}
