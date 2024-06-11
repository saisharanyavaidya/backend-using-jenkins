pipeline {
    agent {
        label 'AGENT-1' 
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment {
        def appVersion = '' //declare global variable here so that this can be used across all stages
    }
    stages {
        stage('read the version') {
            steps {
                script {
                    def packageJson = readJSON file : 'package.json'
                    appVersion = packageJson.version
                    echo "application version --: $appVersion"
                }
            }
        }
        stage('install dependencies') {
            steps {
                sh """
                    npm install
                    ls -ltr
                    echo "application version ##: $appVersion"
                """
            }
        }
        stage('Build') {
            steps {
                sh """
                    zip -r -q backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                    ls -ltr
                """
            }
        }
        
    }

    post {
        always {
            echo "I will always run even when pipeline is success or failure"
            deleteDir()
        }
        success {
            echo "I will run when pipeline is success"
        }
        failure {
            echo "I will run when pipeline is failure"
        }
    }
}