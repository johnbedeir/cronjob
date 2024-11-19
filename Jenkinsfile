// pipeline {
//     agent any

//     stages {
//         stage('Update File') {
//             steps {
//                 withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
//                     script {
//                         // Clone the repository
//                         checkout([$class: 'GitSCM', branches: [[name: '*/test']], userRemoteConfigs: [[url: "https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git"]]])

//                         // Ensure correct branch is checked out
//                         sh '''
//                             git remote set-url origin https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git
//                             git checkout test || git checkout -b test
//                         '''

//                         // Update the file with the current date
//                         def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
//                         writeFile file: 'update_me.yaml', text: "LAST_UPDATE: ${currentDate}\n"

//                         // Stage, commit, and push changes
//                         sh '''
//                             git add update_me.yaml
//                             git commit -m "Updated LAST_UPDATE in update_me.yaml"
//                             git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git HEAD:test
//                         '''

//                         // Wait for the branch push to reflect
//                         sleep(time: 15, unit: 'SECONDS')

//                         // Authenticate GitHub CLI
//                         sh '''
//                             echo "${GIT_TOKEN}" | gh auth login --with-token
//                         '''

//                         // Create the pull request
//                         sh '''
//                             gh pr create \
//                             --title "Update from test branch" \
//                             --body "This pull request contains updates from the test branch." \
//                             --base main \
//                             --head test
//                         '''

//                         // Merge the pull request
//                         sh '''
//                             gh pr merge --merge
//                         '''
//                     }
//                 }
//             }
//         }
//     }
// }


pipeline {
    agent any

    stages {
        stage('Commit with Custom Date') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
                    script {
                        // Clone the repository
                        checkout([$class: 'GitSCM', branches: [[name: '*/test']], userRemoteConfigs: [[url: "https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git"]]])

                        // Ensure correct branch is checked out
                        sh '''
                            git remote set-url origin https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git
                            git checkout test || git checkout -b test
                            git pull
                        '''

                        // Generate a custom date for the commit (e.g., 30 days ago)
                        def customDate = sh(script: 'date -d "365 days ago" +"%Y-%m-%d %H:%M:%S"', returnStdout: true).trim()

                        // Update the file
                        def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        writeFile file: 'update_me.yaml', text: "LAST_UPDATE: ${currentDate}\n"

                        // Stage, commit, and push changes with custom commit dates
                        sh """
                            export GIT_AUTHOR_DATE='${customDate}'
                            export GIT_COMMITTER_DATE='${customDate}'
                            git add update_me.yaml
                            git commit -m "Updated LAST_UPDATE in update_me.yaml"
                            git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git HEAD:test
                        """

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
