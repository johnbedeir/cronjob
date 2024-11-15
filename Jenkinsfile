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
                        cd cronjob && git pull git pull https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git main
                    '''
                    script {
                        def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        sh "echo 'LAST_UPDATE: ${currentDate}' > update_me.yaml"
                    }
                    sh "git add update_me.yaml"
                    sh 'git commit -m "Updated LAST_UPDATE in update_me.yaml"'

                    sh "git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/johnbedeir/cronjob.git HEAD:main"

                    sleep(time: 30, unit: 'SECONDS')
                    
                    // Create a pull request from dev to main
                    //sh "gh pr create --title 'Update from dev branch' --body 'This pull request contains updates from the dev branch.' --base main --head dev"

                    // Merge the pull request
                    //sh "gh pr merge dev --merge"
                    }
                }
            }
        }
    }
}
