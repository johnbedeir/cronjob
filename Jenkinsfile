pipeline {
    agent any
    
    stages {
        stage('Update File') {
            steps {
                script {
                    // Clone the repository
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/johnbedeir/cronjob.git']]])
                    
                    // Replace the content of the file with the current date and time
                    sh "git clone https://github.com/johnbedeir/cronjob.git"
                    sh "cd cronjob"
                    sh "echo 'LAST_UPDATE: $(date +\"%A %B %d %Y at %I:%M:%S%p\")' > update_me.yaml"
                    sh "git add update_me.yaml"
                    sh 'git commit -m "Updated LAST_UPDATE in update_me.yaml"'
                    sh "git push origin main"
                }
            }
        }
    }
}
