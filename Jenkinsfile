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
                sh 'docker build -t berthiny/test .'     
            }
        }
        
        stage('Test image') {
            steps {
                sh 'echo "Tests passed"'
            }
        }
        
        stage('Push image') { 
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerhubPassword', usernameVariable: 'dockerhubUser')]) {
        	        sh 'docker login -u ${env.dockerhubUser} -p ${env.dockerhubPassword}'
                    sh 'docker push berthiny/test'
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
