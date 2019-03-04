#!/usr/bin/env groovy
def projectName = currentBuild.projectName
def version = "0.1.0"
pipeline {
    agent { 
    node { label 'master' }
     }
     
    environment {
        CI = 'true'
        JENKINS_CRUMB = 'curl user username:password "<jenkins-url>/crumbIssuer/api/xml?xpath=concat(//crumbRequestField, \":\",//crumb)"'
		
    }
    stages {
        stage("Checkout") {
            steps {
                load "environmentVariables.groovy"
                echo "${env.DEV_SCM_REPOSITORY}"
                echo "${env.DEV_SCM_BRANCH}"
                git(url: "${env.DEV_SCM_REPOSITORY}", branch: "${env.DEV_SCM_BRANCH}", poll: true)
            }
        }

        stage("Build") {
        steps {
                echo "Building.."
                //sh 'mvn org.codehaus.mojo:exec-maven-plugin:exec'
               sh 'npm install'
            }
		}
		
		stage("SonarQube analysis") {
			steps {
				withSonarQubeEnv('SonarQubeDev') {
      			sh 'npm run sonar-scanner'
    			}
    			}
    			}
		
		
		/*stage("SonarQube analysis") {
			steps {
				withSonarQubeEnv('SonarQubeDev') {
      			sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar'
    			}
    			}
    		
  			}*/
  			
  			/*stage("SonarQube Analysis") {
        		steps {
        		//Defines your NodeJS environment and Tells Sonar where to run the code. Available under Manage Jenkins > Global tool Configuration
        		//NodeJS plugin required to configure this
            nodejs(nodeJSInstallationName: 'NodeJSLocal', configId: '') {
                // sonar script to run in a NodeJS Module
                script {
                    withSonarQubeEnv('SonarQubeDev') {
                    def scannerHome = tool 'sonarScanner';
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.login=[my analysis token]"
                }
            }
            }
        }
    }*/	
		
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarQube Scanner for Jenkins 2.7+
                    waitForQualityGate abortPipeline: true
                }
            }
        }

         stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }

//        stage('Deliver') {
//            steps {
//                sh './jenkins/scripts/deliver.sh'
//                input message: 'Finished using the web site? (Click "Proceed" to continue)'
//                sh './jenkins/scripts/kill.sh'
//            }
//        }
        
        stage('Pack artefacts'){
            steps {
            script {
                def npmPack = sh(returnStdout:true, script:'npm pack').trim()
            	sh "echo ${npmPack}"
            }   
            }
        }

        
        stage('Archive/Upload Artefact to Nexus'){
 
                steps{
                      nexusArtifactUploader(
						    nexusVersion: 'nexus3',
						    protocol: 'http',
						    nexusUrl: 'localhost:8081',
						    groupId: 'com.example',
						    version: version,
						    repository: 'DynamicsDeveloperReleases',
						    credentialsId: 'jenkins-nexus-authentication',
						    artifacts: [
						        [artifactId: projectName,
						         classifier: '',
						         file: projectName + version + '.tgz',
						         type: 'tgz']
						    			]
 									)
                                          }
                                       }
    								}

    post {
        always {
          cleanWs()  
        }
        
        failure {
             //mail to: 'someone@somewhere.com' , subject: "Status of pipeline: ${currentBuild.fullDisplayName}" , body: "${env.BUILD_URL} has result ${currentBuild.result}"
        	echo "${currentBuild.projectName} has failed at ${env.BUILD_URL}"
        }
       
    }
        
    }
    

		