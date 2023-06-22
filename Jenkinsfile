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
                    rm -rf cronjob
                    '''
                }
            }
        }
    }
}
