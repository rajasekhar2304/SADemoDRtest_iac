pipeline {
    agent any
    stages {
        stage("SCM Checkout") {
            steps {
                git branch: 'master',
                    credentialsId: "RajaGitHubAccess",            
                    url: 'https://github.com/rajasekhar2304/my-react-app.git'
                dir("SADemoDRtest_iac") {
                    git branch: 'master',
                    credentialsId: "RajaGitHubAccess",
                    url: 'https://github.com/rajasekhar2304/SADemoDRtest_iac.git'
                }
            }  
        }             
        
        stage("SonarQube analysis") 
		{
			environment 
			{
				scannerHome = tool 'SonarQubeScanner-4.7.0'
			}
			steps 
			{
				withSonarQubeEnv('sonarqube-8.9.9') 
				{
					sh ''' 
					${scannerHome}/bin/sonar-scanner \
                	-D sonar.projectKey=my-react-app
					'''
				}
			}
		}

    	stage("Quality Gate Status Check")
		{
			steps
			{
				timeout(time: 1, unit: 'HOURS')
				{
					script
					{
						def qg = waitForQualityGate()
						if (qg.status != 'OK')
						{
						error "Pipeline aborted due to quality gate failure: ${qg.status}"
						}
					}
									
				}

			}

    	} 
        stage('Build') {
            steps {
                sh "npm install"
                sh "npm run build"               
            }
        }
        stage('Directory Cleaning') {
            steps {
                ansiblePlaybook become: true,
                    credentialsId: 'JENKINS_APP_SERVER',
                    disableHostKeyChecking: true, 
                    installation: 'ansible', 
                    inventory: 'SADemoDRtest_iac/inventories/dev.inv', 
                    limit: 'jenkinsServer', 
                    playbook: 'SADemoDRtest_iac/appDeploy.yml',
                    tags: 'clean_files'
            }
        }
        stage('copy files') {
            steps {
                ansiblePlaybook become: true,
                    credentialsId: 'JENKINS_APP_SERVER',
                    disableHostKeyChecking: true, 
                    installation: 'ansible', 
                    inventory: 'SADemoDRtest_iac/inventories/dev.inv', 
                    limit: 'jenkinsServer', 
                    playbook: 'SADemoDRtest_iac/appDeploy.yml',
                    tags: 'copy_files'
            }
        }
        // stage('Deploy') {
        //     steps {
        //         ansiblePlaybook become: true, 
        //             credentialsId: 'JENKINS_APP_SERVER',
        //             disableHostKeyChecking: true, 
        //             installation: 'ansible', 
        //             inventory: 'SADemoDRtest_iac/inventories/dev.inv', 
        //             limit: 'jenkinsServer', 
        //             playbook: 'SADemoDRtest_iac/appDeploy.yml',
        //             tags: 'eb_deploy'                
        //     }
        // }
        
    }
}