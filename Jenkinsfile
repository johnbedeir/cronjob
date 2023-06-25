pipeline {
    agent any
    

    environment {
        GH_TOKEN = credentials('github-token')
    }

    stages {
        stage('Update File') {
            steps {
                script {
                    // Clone the repository
                    checkout([$class: 'GitSCM', branches: [[name: '*/dev']], userRemoteConfigs: [[url: 'https://github.com/johnbedeir/cronjob.git']]])
                    
                    // Replace the content of the file with the current date and time
                    sh "cd cronjob"
                    sh "git pull https://github.com/johnbedeir/cronjob.git dev"
                    script {
                        def currentDate = sh(script: 'date +"%A %B %d %Y at %I:%M:%S%p"', returnStdout: true).trim()
                        sh "echo 'LAST_UPDATE: ${currentDate}' > update_me.yaml"
                    }
                    sh "git add update_me.yaml"
                    sh 'git commit -m "Updated LAST_UPDATE in update_me.yaml"'
                    sh "git push origin HEAD:dev"

                    // Create a pull request from dev to main
                    withCredentials([string(credentialsId: 'github-token', variable: 'GH_TOKEN')]) {
                        script {
                            def prTitle = "Update from dev branch"
                            def prBody = "This pull request contains updates from the dev branch."
                            sh "GH_TOKEN=${env.GH_TOKEN} gh pr create --title '${prTitle}' --body '${prBody}' --base main --head dev"
                        }
                    }
                    
                    // Merge the pull request
                    withCredentials([string(credentialsId: 'github-token', variable: 'GH_TOKEN')]) {
                        script {
                            sh "GH_TOKEN=${env.GH_TOKEN} gh pr merge --auto --merge --delete-branch --head dev --base main"
                        }
                    }
                }
            }
        }
    }
}
