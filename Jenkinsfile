pipeline {
    agent any
    
    stages {
        stage('Update File') {
            steps {
                script {
                    // Clone the repository
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/johnbedeir/cronjob.git' ]]])

                    // Replace the content of the file with the current date and time
                    sh '''
                    git clone https://github.com/johnbedeir/cronjob.git
                    cd cronjob
                    echo 'LAST_UPDATE: $(date +"%A %B %d %Y at %I:%M:%S%p")' > update_me.yaml
                    git add update_me.yaml
                    git commit -m "Updated LAST_UPDATE in update_me.yaml"
                    git push origin main
                    rm -rf cronjob
                    '''
                }
            }
        }
    }
}
