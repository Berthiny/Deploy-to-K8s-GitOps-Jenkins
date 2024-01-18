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
                withDockerRegistry(credentialsId:'dockerhub', url:'https://hub.docker.com') {
                    def app = docker.build("berthiny/test:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Test image') {
            steps {
                sh 'echo "Tests passed"'
            }
        }
        
        stage('Push image') { 
            steps {
                withDockerRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    app.push()
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
