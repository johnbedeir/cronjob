pipeline {
    agent any

    stages {
        stage('Update File') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
                script {
                    // Clone the repository
                    //checkout([$class: 'GitSCM', branches: [[name: '*/dev']], userRemoteConfigs: [[url: 'https://github.com/johnbedeir/cronjob.git']]])
                    sh '''
                        cd cronjob && git pull https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git test
                    '''
                    script {
                        def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        sh "echo 'LAST_UPDATE: ${currentDate}' > update_me.yaml"
                    }
                    sh "git add update_me.yaml"
                    sh 'git commit -m "Updated LAST_UPDATE in update_me.yaml"'

                    sh "git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git HEAD:test"

                    sleep(time: 30, unit: 'SECONDS')
                    
                    // Create a pull request from dev to main
                    sh '''
                        cd cronjob && gh pr create --title 'Update from test branch' --body 'This pull request contains updates from the test branch.' --base main --head test
                    '''

                    // Merge the pull request
                    sh '''
                        cd cronjob && gh pr merge test --merge
                    '''
                    }
                }
            }
        }
    }
}
