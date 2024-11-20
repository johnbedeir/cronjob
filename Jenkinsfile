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

                        def randomDayOfYear = sh(script: 'echo $(( $(date "+%s") % 365 + 1 ))', returnStdout: true).trim()
                        def commitDate = sh(script: "date -d '2022-01-01 +${randomDayOfYear} days' +'%Y-%m-%dT%H:%M:%S'", returnStdout: true).trim()
                        
                        // Ensure correct branch is checked out
                        sh '''
                            git remote set-url origin https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git
                            git checkout test || git checkout -b test
                            git pull https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git test
                        '''

                    script {
                        //def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        sh "echo 'LAST_UPDATE: ${commitDate}' > update_me.yaml"
                        def ISSUE = sh(script: 'gh issue create --title "LAST_UPDATE" --body "Update LAST_UPDATE"', returnStdout: true).trim()
                        def ISSUE_NUMBER = sh(script: "basename ${ISSUE}", returnStdout: true).trim()
                        sh """
                        git add update_me.yaml
                        GIT_COMMITTER_DATE="${commitDate}" GIT_AUTHOR_DATE="${commitDate}" git commit -m "Fix #${ISSUE_NUMBER} in update_me.yaml"
                        git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git HEAD:test
                        """
                    }
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
