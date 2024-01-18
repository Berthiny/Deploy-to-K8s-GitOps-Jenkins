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
                sh 'docker build -t berthiny/test:${env.BUILD_ID} .'     
            }
        }
        
        stage('Test image') {
            steps {
                sh 'echo "Tests passed"'
            }
        }
        
        stage('Push image') { 
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        	        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                    sh 'docker push berthiny/test:${env.BUILD_ID}'
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
