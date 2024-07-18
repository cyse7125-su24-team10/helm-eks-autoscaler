pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git credentialsId: 'github-pat', branch: 'main', url: 'https://github.com/cyse7125-su24-team10/helm-eks-autoscaler.git'
            }
        }
        stage('helm lint') {
            steps {
                script {
                    sh "helm lint ./"
                    sh "helm template ./"
                }
            }
        }
        stage('release') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-pat', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                    script {
                        sh "npm install semantic-release-helm"
                        sh "npx semantic-release"
                    }
                }
            }
        }
    }
}