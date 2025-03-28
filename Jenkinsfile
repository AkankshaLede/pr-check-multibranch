pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-pat-token') // Ensure you have a Jenkins credential with this ID
        GITHUB_REPO = 'AkankshaLede/pr-check-multibranch' // Your repository name
        BASE_BRANCH = 'main' // The branch you want to create PRs against
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check for Changes') {
            steps {
                script {
                    def changedFiles = sh(script: 'git diff --name-only origin/${env.BRANCH_NAME}', returnStdout: true).trim().split('\n')
                    boolean configChanged = changedFiles.any { it.endsWith('.yaml') || it.endsWith('.ini') || it.endsWith('.json') || it.contains('config/') } // Add your config file patterns

                    if (changedFiles) {
                        echo "Changes detected in the following files:\n${changedFiles.join('\n')}"
                        env.HAS_CHANGES = true
                        env.CONFIG_CHANGED = configChanged
                    } else {
                        echo "No changes detected."
                        env.HAS_CHANGES = false
                        env.CONFIG_CHANGED = false
                    }
                }
            }
        }

        stage('Create Pull Request (if changes exist and not on base branch)') {
            when {
                expression { env.HAS_CHANGES == true && env.BRANCH_NAME != env.BASE_BRANCH }
            }
            steps {
                script {
                    sh """
                    set +x
                    echo "$GITHUB_TOKEN" | gh auth login --with-token
                    PR_EXISTS=$(gh pr list --base "${env.BASE_BRANCH}" --head "${env.BRANCH_NAME}" --json number --jq '.[0].number' 2>/dev/null)

                    if [ -z "$PR_EXISTS" ]; then
                        echo "No existing pull request found. Creating a new one."
                        gh pr create --title "Automated PR from ${env.BRANCH_NAME}" --body "Automated pull request triggered by changes in branch ${env.BRANCH_NAME}." --base "${env.BASE_BRANCH}" --head "${env.BRANCH_NAME}"
                    else
                        echo "Pull request #$PR_EXISTS already exists for branch ${env.BRANCH_NAME} targeting ${env.BASE_BRANCH}."
                    fi
                    """
                }
            }
        }

        stage('Trigger GitHub Actions (after PR creation or on main)') {
            steps {
                echo "Triggering GitHub Actions workflow for branch: ${env.BRANCH_NAME}"
                // Your GitHub Actions workflow will automatically run on new commits and pull requests
                // You might want to add specific steps here if you need to interact with the GitHub Actions workflow from Jenkins
            }
        }

        stage('Build') {
            when {
                expression { env.HAS_CHANGES == true }
            }
            steps {
                echo "Building on branch: ${env.BRANCH_NAME}"
                // Add your build steps here (e.g., mvn clean install, npm install, etc.)
            }
        }

        stage('Test') {
            when {
                expression { env.HAS_CHANGES == true }
            }
            steps {
                echo "Testing on branch: ${env.BRANCH_NAME}"
                // Add your test steps here (e.g., mvn test, npm test, etc.)
            }
        }

        stage('Deploy (on main with changes)') {
            when {
                expression { env.BRANCH_NAME == 'main' && env.HAS_CHANGES == true }
            }
            steps {
                echo "Deploying from branch: ${env.BRANCH_NAME}"
                // Add your deployment steps here
            }
        }
    }
}