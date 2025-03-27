pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo "Building on branch: ${env.BRANCH_NAME}"
                // Add your build steps here (e.g., mvn clean install, npm install, etc.)
            }
        }
        stage('Test') {
            steps {
                echo "Testing on branch: ${env.BRANCH_NAME}"
                // Add your test steps here (e.g., mvn test, npm test, etc.)
            }
        }
        stage('Deploy') {
            when {
                branch 'main' // Only run deploy on the main branch
            }
            steps {
                echo "Deploying from branch: ${env.BRANCH_NAME}"
                // Add your deployment steps here
            }
        }
    }
}