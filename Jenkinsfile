pipeline {
  agent none
  parameters { 
  
	choice(name: 'BUILD_TYPE', choices: ['COR', 'DEV', 'REL'], description: '')
	choice(name: 'SLDSERVER', choices: ['10.58.8.34'], description: '')   
	choice(name: 'AGENTSERVER', choices: ['10.58.8.34'], description: '')   
	 string(name: 'SLD_BUILD_PATH', defaultValue: '', description: 'If SLD_BUILD_PATH=null by default using latest build according BUILD_TYPE
For example: \\10.59.60.216\Build_Storage\builds_cn\SBO\B1ANY\B1OD_1.1_COR\2019-04-01_13-47-22_1618751') 
 string(name: 'dbuser', defaultValue: 'sa', description: '') 
  string(name: 'dbpass', defaultValue: 'SAPB1Admin', description: '') 

  }
  stages {
    stage('SyncGit'){ 
		  agent {
            node {
              label 'master'
            }
			}
          steps {
			checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '99c942e8-6b5f-4409-b7b8-d495ee904286', url: 'https://github.wdf.sap.corp/AlvinWang/CloudJenkins.git']]])
          }
    }
	
        stage('BuildCopy') {
          agent {
            node {
              label 'master'
            }

          }
          steps {
            retry(count: 3) {
    
            bat '%WORKSPACE%\\CopySLDBuild.bat'            
          }
        }
        }
		
		
		stage('InstallSLD') {
		 agent {
            node {
              label '34WindowSLD'
            }

          }
          steps {
            retry(count: 3) {
    		bat 'echo %BASE%'
            bat '%BASE%\\InstallSLD.bat'            
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
		bat 'echo %WORKSPACE%'
		bat 'echo %AGENTSERVER%'
		bat 'echo %GITLOCALAGENTSERVER%'
      }
    }
  }
}