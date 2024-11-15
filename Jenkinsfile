pipeline {
    agent any

    stages {
        stage('Update File') {
            steps {
                script {
                    // Clone the repository
                    //checkout([$class: 'GitSCM', branches: [[name: '*/dev']], userRemoteConfigs: [[url: 'https://github.com/johnbedeir/cronjob.git']]])
                    sh "git clone https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git"
                    // Replace the content of the file with the current date and time
                    sh "git pull https://github.com/johnbedeir/cronjob.git dev"
                    script {
                        def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        sh "echo 'LAST_UPDATE: ${currentDate}' > update_me.yaml"
                    }
                    sh "git add update_me.yaml"
                    sh 'git commit -m "Updated LAST_UPDATE in update_me.yaml"'
                    sh "git push origin HEAD:dev"

                    sleep(time: 30, unit: 'SECONDS')
                    
                    // Create a pull request from dev to main
                    sh "gh pr create --title 'Update from dev branch' --body 'This pull request contains updates from the dev branch.' --base main --head dev"

                    // Merge the pull request
                    sh "gh pr merge dev --merge"
                }
            }
        }
    }
}
