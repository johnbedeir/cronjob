pipeline {
    agent any
    

    environment {
        GH_USERNAME = credentials('github-credentials').username
        GH_PASSWORD = credentials('github-credentials').password
        GH_TOKEN = "${GH_USERNAME}:${GH_PASSWORD}"
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
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GH_USERNAME', passwordVariable: 'GH_PASSWORD')]) {
                        withEnv(['GH_TOKEN=${env.GH_TOKEN}']) {
                            script {
                                def prTitle = "Update from dev branch"
                                def prBody = "This pull request contains updates from the dev branch."
                                sh "gh pr create --title '${prTitle}' --body '${prBody}' --base main --head dev"
                            }
                        }
                    }

                    // Merge the pull request
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GH_USERNAME', passwordVariable: 'GH_PASSWORD')]) {
                        withEnv(['GH_TOKEN=${env.GH_TOKEN}']) {
                            script {
                                sh "gh pr merge --auto --merge --delete-branch --head dev --base main"
                            }
                        }
                    }
                }
            }
        }
    }
}
