@Library('devops-shared-lib')_


pipeline {
    environment {
        buildName = "${env.JOB_NAME}"
        buildNumber = "${env.BUILD_NUMBER}"
        branchName = "${env.BRANCH_NAME}"
        HOME = "."
    }

	options {
		  disableConcurrentBuilds()
		  buildDiscarder(logRotator(numToKeepStr:'5'))
		  timeout(time: 10, unit: 'MINUTES')
		}
		
    agent {
        docker {
            alwaysPull false
            image 'zazukoians/node-java'
        }
    }

    stages {
        stage('Initial') {
            steps {
			  dir('dist') {
                script {
					        sendNotifications 'STARTED'
					        deleteDir()
                            npmUtils.echoDevTools(buildName, branchName)
                }
				}
            }
        }

        stage('Build Node. JS project') {
            steps {
                script {
                    npmUtils.install()
                    npmUtils.build()                
                }
            }
        }
		
		stage('Archive dist folder') {
					steps {
						script {    
							echo 'Zip dist content and archive for later usage'
                   		    fileOperations([fileZipOperation(folderPath: 'dist', outputFolderPath: '')])
							archiveArtifacts artifacts: 'dist.zip', excludes: '' , onlyIfSuccessful: true
						}
					}
        }


    }
    post {
         always {
		 script{
			envProps.cleanWorkspace()
            sendNotifications currentBuild.result
			}
        }
    }
}