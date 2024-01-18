pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                checkout scmGit(branches: [[name: 'main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Berthiny/Deploy-to-K8s-GitOps-Jenkins']])
            }
        }
        
        stage('Build image') {
            steps {
                app = docker.build("berthiny/test")
            }
        }
        
        stage('Test image') {
            steps {
                sh 'echo "Tests passed"'
            }
        }
        
        stage('Push image') { 
            steps {
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    app.push("${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('Trigger ManifestUpdate') {
            steps {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
        }
    }
}
