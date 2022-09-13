pipeline {
    agent any
    environment {
        PATH = "$PATH:/usr/share/maven/bin"
    }
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
        stage('Build') {
            steps {
                sh "npm install"
                sh "npm run build"               
            }
        }
        stage('SonarQube analysis') {
            steps{
                withSonarQubeEnv('sonarqube-8.9.9') {      
                    sh "sonar-scanner \
                    -Dsonar.projectKey=nodejs-project \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://3.89.19.217:9000 \
                    -Dsonar.login=7c8acc9013465ed3b492e23b800e1de1ffacb34a"
                }
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