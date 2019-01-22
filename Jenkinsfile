#!/usr/bin/env groovy
pipeline {
    agent { node { label 'master' } }
    environment {
        CI = 'true'
    }

    stages {
        stage("Checkout") {
            steps {
                git(url: 'file:///Users/munene/git/simple-node-js-react-npm-app/.git', branch: 'master', poll: true)
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

}

		