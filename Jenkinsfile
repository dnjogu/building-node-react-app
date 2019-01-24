#!/usr/bin/env groovy
pipeline {
    agent { node { label 'master' }
     }
    environment {
        CI = 'true'
    }

    stages {
        stage("Checkout") {
            steps {
                load "environmentVariables.groovy"
                git(url: 'env.DEV_SCM_REPOSITORY', branch: 'env.DEV_SCM_BRANCH', poll: true)
            }
        }

        stage("Build") {
        steps {
                echo "Building.."
                sh 'npm install'
            }
		}

         stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }

        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }

    }

    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'I succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}

		