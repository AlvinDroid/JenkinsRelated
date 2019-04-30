System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")
    def jobName = currentBuild.fullDisplayName

pipeline {
  agent none
  parameters { 
  
	choice(name: 'BUILD_TYPE', choices: ['DEV', 'COR', 'REL'], description: '')
	choice(name: 'SLDSERVER', choices: ['10.58.8.34'], description: '')   
	choice(name: 'AGENTSERVER', choices: ['10.58.8.34'], description: '')  
	choice(name: 'SLD_TYPE', choices: ['Window'], description: '')   
	
	 string(name: 'SLD_BUILD_PATH', defaultValue: '', description: 'If SLD_BUILD_PATH=null by default using latest build according BUILD_TYPE. For example:\\\\10.59.60.216\\Build_Storage\\builds_cn\\SBO\\B1ANY\\B1OD_1.1_COR\\2019-04-01_13-47-22_1618751') 
 string(name: 'dbuser', defaultValue: 'sa', description: '') 
  string(name: 'dbpass', defaultValue: 'SAPB1Admin', description: '')
    	 string(name: 'Domain', defaultValue: 'mocca', description: 'Domain name')
  	 string(name: 'DomainUsername', defaultValue: 'b1admin', description: 'Domain admin for setup environment')
  	   	 string(name: 'DomainPassowrd', defaultValue: 'Initial1', description: 'Domain admin password' ) 


  }
  
  
   environment { 
    //def mailRecipients = "alvin.wang01@sap.com;eric.li03@sap.com;wen.cheng01@sap.com;shirley.yang@sap.com;peilin.qin@sap.com"
    def mailRecipients = "alvin.wang01@sap.com"

    def DESTPATH = "/data/wwwroot"
    def SRCPATH = "~/workspace/linuxea-2"
	def GitJnkinsPath = "C:\\git\\CloudJenkinsWindowSLD"

    }
  stages {
    stage('SyncGit'){ 
		  agent {
            node {
            label 'master'
            customWorkspace GitJnkinsPath

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
			  customWorkspace GitJnkinsPath

            }

          }
          steps {
            retry(count: 3) {
    
            bat '%WORKSPACE%\\CopySLDBuild.bat'            
          }
        }
        }
		
		
		stage('Uninstall SLD&SLDAgent') {
			 agent {
            node {
              label '34WindowSLD'
            }
          }
		
	
          steps {
            retry(count: 3) {

    		bat 'echo %BASE%'
    		echo ' -----------------Start SLD stop and uninstall -----------------'
    		bat "%BASE%\\%AGENTSERVER%\\elevate-1.3.0-redist\\bin.x86-64\\elevate.exe -c  sc stop SLD"
			sleep time: 1, unit: 'MINUTES'
            bat '%BASE%\\%SLDSERVER%\\UninstallSLD.bat'
            echo ' -----------------Wait SLD stop and uninstall -----------------'
			sleep time: 3, unit: 'MINUTES'
            bat 'osql -S %SLDSERVER% -U%dbuser% -P%dbpass% -i %BASE%\\%SLDSERVER%\\DropSLDDATA.sql'
            bat '%BASE%\\%AGENTSERVER%\\elevate-1.3.0-redist\\bin.x86-64\\elevate.exe -k %BASE%\\%AGENTSERVER%\\UninstallAgent.bat'
            echo ' -----------------Wait SLD Agent stop and uninstall -----------------'
			sleep time: 1, unit: 'MINUTES'
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
    		bat 'echo %BASE%'
            bat '%BASE%\\%SLDSERVER%\\InstallSLD.bat'   
            echo ' -----------------Wait SLD service start -----------------'
            sleep time: 2, unit: 'MINUTES'
			}
		}
		
		stage('InstallSLDAgent') {
		 agent {
            node {
              label '34WindowSLD'
            }

          }
          steps {
    		bat 'echo %BASE%'
            bat '%BASE%\\%AGENTSERVER%\\InstallAgent.bat'            
            bat '''MsiExec.exe /x {32FA0949-F55E-4A3F-AD29-74D0FC5F3243} /qn
                reg delete HKEY_LOCAL_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Uninstall\\InstallShield_{32FA0949-F55E-4A3F-AD29-74D0FC5F3243} /f
                for /D %%i in ("C:\\Program Files (x86)\\SAP\\SAP Business One BAS GateKeeper\\*") do rmdir /s/q "%%i"
                exit 0'''
			bat 'echo -----------------Clean storages and then Uninstall BA begin-----------------'  
			bat '''for /D %%i in (\\\\10.58.8.20\\SLDSourceDemo34\\TenantsStorageTarget\\*) do rmdir /s/q "%%i"
					for /D %%i in (\\\\10.58.8.20\\SLDSourceDemo34\\TenantsStorageBase\\*) do rmdir /s/q "%%i"
					for /D %%i in (\\\\10.58.8.20\\SLDSourceDemo34\\SharefolderRepositoriesBase\\*) do rmdir /s/q "%%i"
					for /D %%i in (\\\\10.58.8.20\\SLDSourceDemo34\\SharefolderRepositoriesTarget\\*) do rmdir /s/q "%%i"
					for /D %%i in (\\\\10.58.8.20\\SLDSourceDemo34\\UserStorage\\*) do rmdir /s/q "%%i"
					REM uninstall BA
					MsiExec.exe /x {32FA0949-F55E-4A3F-AD29-74D0FC5F3243} /qn
					reg delete HKEY_LOCAL_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Uninstall\\InstallShield_{32FA0949-F55E-4A3F-AD29-74D0FC5F3243} /f
					for /D %%i in ("C:\\Program Files (x86)\\SAP\\SAP Business One BAS GateKeeper\\*") do rmdir /s/q "%%i"
					exit 0'''
			bat 'echo -----------------Clean storages and then Uninstall BA begin-----------------'  
			
			
		bat 'echo -----------------Delete related DBs begin-----------------'  
		bat 'osql -S %SLDSERVER% -U%dbuser% -P%dbpass% -i %BASE%\\%SLDSERVER%\\DropSBOCOMMONTENANTS.sql'
        bat 'osql -S %SLDSERVER%\\instance2 -U%dbuser% -P%dbpass% -i %BASE%\\%SLDSERVER%\\DropSBOCOMMONTENANTS.sql'
		bat 'echo -----------------Delete related DBs end-----------------'  

        }
		}
		
		
		
		
	stage('PreconditionAD') {
      agent {
        node {
          label 'ADWindow'
        }

      }
      steps {
			bat '''net USER sqljerry /delete
			net USER sqltest1 /delete
			net USER sqltest2 /delete
			net USER sqloperator /delete
			net USER sqlresellerta /delete
			net USER sqlresellerta2 /delete
			net USER sqlresellerta_2fa /delete
			net USER sqlresellerta2_2fa /delete
			exit 0'''

      }
    }
	
	
	
	
		
    stage('Test') {
      agent {
        node {
          label 'master'
        }

      }
      steps {
        timeout(180){
        bat 'echo -----------------ComponentsdoRegisterTest begin-----------------'
		bat '%TA_ROOT_WORKSPACE%\\ComponentsdoRegisterTest.bat'            
		bat 'echo -----------------ComponentsdoRegisterTest end-----------------'
		checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '99c942e8-6b5f-4409-b7b8-d495ee904286', url: 'https://github.wdf.sap.corp/AlvinWang/CloudJMeter.git']]])
        bat 'echo -----------------Copy CL txt to workspace begin-----------------'
        bat 'copy "%GitJnkinsPath%\\Build\\%BUILD_TYPE%\\B1OD\\info.txt" "%WORKSPACE%\\CL_info_%BUILD_TIMESTAMP%.txt"'
        bat 'echo -----------------Copy CL txt to workspace end-----------------'
		bat 'echo -----------------jMeter API test begin-----------------'
		bat '''cd %WORKSPACE%
		    ant'''
		bat 'echo %GITLOCALAGENTSERVER%'
		bat 'echo -----------------jMeter API test end-----------------'
		publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: '\\html68\\', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
		perfReport errorFailedThreshold: 0, errorUnstableThreshold: 0, failBuildIfNoResultFile: false, relativeFailedThresholdNegative: 0.0, relativeFailedThresholdPositive: 0.0, relativeUnstableThresholdNegative: 0.0, relativeUnstableThresholdPositive: 0.0, sourceDataFiles: 'TestReport.jtl'

      }
          
      }
    }
	

	
  }
  	post  {
	      always {

    emailext body: '''${JELLY_SCRIPT, template="htmlJmeterDailyTASQL.jelly"}''',
        mimeType: 'text/html',
        subject: "[Jenkins] ${jobName}",
        to: "${mailRecipients}",
        replyTo: "${mailRecipients}",
        recipientProviders: [[$class: 'CulpritsRecipientProvider']]
      }
}
}
