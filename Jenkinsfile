#!/usr/bin/env groovy
pipeline {
    agent { node { label 'master' } }

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
    }

}

		