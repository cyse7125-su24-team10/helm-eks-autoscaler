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
           stage('mirror image') {
            steps {
                    script {
                        def latestTag = sh(script: 'git describe --abbrev=0 --tags', returnStdout: true).trim()
                        echo "Using release tag: ${latestTag}"
                        sh 'echo $DOCKER_PWD | docker login -u $DOCKER_ID --password-stdin'
                        sh 'docker pull --platform linux/amd64 registry.k8s.io/autoscaling/cluster-autoscaler:v1.30.0'
                        sh "docker tag registry.k8s.io/autoscaling/cluster-autoscaler:v1.30.0 ${registry}:${latestTag}"
                        sh "docker push ${registry}:${latestTag}"
                    }
                }
        }
    }
}