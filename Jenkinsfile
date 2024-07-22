pipeline {
        environment {
        registry = "vkneu7/eks-autoscaler"
        DOCKER_ID = 'vkneu7'
        DOCKER_PWD = credentials('DOCKER_PWD')
        //imageName = "webapp-consumer"
    }
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
                        sh 'docker buildx rm newbuilderx || true'
                        sh 'docker buildx create --use --name newbuilderx --driver docker-container'
                        sh "docker buildx build --file Dockerfile --platform linux/amd64,linux/arm64 -t ${registry}:${latestTag} --push ."
                        sh 'docker buildx rm newbuilderx'
                    }
                }
        }
    }
}