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
        def nexusUrl = 'nexus.avyan.site:8081'
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
        stage('Nexus Artifact Upload'){
            steps{
                script{
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "backend" ,
                            classifier: '',
                            file: "backend-" + "${appVersion}" + '.zip',
                            type: 'zip']
                        ]
                    )
                }
            }
        }
        stage('Deploy'){
            steps{
                script{
                    def params = [
                        string(name: 'appVersion', value: "${appVersion}") //these params will be used in backend-deploy-using-jenkins project
                    ]
                    build job: 'backend-deploy-using-jenkins', parameters: params, wait: false // this will call deploy project
                }
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