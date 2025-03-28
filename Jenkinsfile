pipeline {
    agent any

    environment {
        GITHUB_URL = "https://github.com/AkankshaLede/pr-check-multibranch.git"
        BRANCH_NAME = "automation1"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Checking out the repository..."
                    git --version
                    git clone --depth=1 $GITHUB_URL repo
                    cd repo
                    git checkout $BRANCH_NAME
                    '''
                }
            }
        }

        stage('Check for Changes') {
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Checking for changes..."
                    cd repo
                    if git diff --quiet; then
                        echo "No changes detected."
                    else
                        echo "Changes detected."
                        exit 1  # Fail the build to indicate changes found
                    fi
                    '''
                }
            }
        }

        stage('Create Pull Request (if needed)') {
            when {
                expression {
                    return currentBuild.result == 'FAILURE' // Runs only if changes are detected
                }
            }
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Creating a Pull Request..."
                    # Add PR creation script here
                    '''
                }
            }
        }

        stage('Trigger GitHub Actions') {
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Triggering GitHub Actions..."
                    curl -X POST -H "Authorization: token YOUR_GITHUB_TOKEN" \
                         -H "Accept: application/vnd.github.everest-preview+json" \
                         "https://api.github.com/repos/AkankshaLede/pr-check-multibranch/actions/workflows/build.yml/dispatches" \
                         -d '{"ref": "automation1"}'
                    '''
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Running build..."
                    # Add build steps here
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Running tests..."
                    # Add test steps here
                    '''
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                script {
                    sh '''
                    #!/bin/bash
                    echo "Deploying application..."
                    # Add deployment steps here
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed!"
        }
        success {
            echo "Pipeline executed successfully!"
        }
    }
}
