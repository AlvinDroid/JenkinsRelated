pipeline {
  agent none
  stages {
    stage('BuildCopy') {
      parallel {
        stage('BuildCopy') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            retry(count: 3) {
              bat(script: 'xcopy "\\\\10.59.60.216\\Build_Storage\\builds_cn\\SBO\\B1ANY\\B1OD_1.1_DEV\\2019-03-13_09-49-55_1613302\\B1OD" "C:\\git\\CloudJenkins\\Build" /s  /e  /y /i', returnStatus: true)
            }

          }
        }
        stage('SyncGit'){ 
		  agent {
            node {
              label 'master'
            }
			}
          steps {
            git(url: 'https://github.wdf.sap.corp/AlvinWang/CloudJenkins.git', branch: 'master', credentialsId: '99c942e8-6b5f-4409-b7b8-d495ee904286')
          }
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