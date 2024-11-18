pipeline {
    agent any

    stages {
        stage('Update File') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
                    script {
                        // Clone the repository and initialize submodules
                        checkout([$class: 'GitSCM', branches: [[name: '*/test']], userRemoteConfigs: [[url: "https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git"]]])

                        // Initialize and update submodules
                        sh '''
                            git submodule update --init --recursive
                            git remote set-url origin https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git
                            git checkout test || git checkout -b test
                        '''

                        // Update the file with the current date
                        def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        writeFile file: 'update_me.yaml', text: "LAST_UPDATE: ${currentDate}\n"

                        // Stage, commit, and push changes
                        sh '''
                            git add update_me.yaml
                            git commit -m "Updated LAST_UPDATE in update_me.yaml"
                            git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git HEAD:test
                        '''

                        // Wait for the branch push to reflect
                        sleep(time: 15, unit: 'SECONDS')

                        // Authenticate GitHub CLI
                        sh '''
                            echo "${GIT_TOKEN}" | gh auth login --with-token
                        '''

                        // Create the pull request
                        sh '''
                            gh pr create \
                            --title "Update from test branch" \
                            --body "This pull request contains updates from the test branch." \
                            --base main \
                            --head test
                        '''

                        // Merge the pull request
                        sh '''
                            gh pr merge --merge
                        '''
                    }
                }
            }
        }
    }
}
