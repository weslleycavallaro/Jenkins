pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script{
                    dockerapp = docker.build("weslley7/guia-jenkins:${env.BUILD_ID}", '-f ./src/Dockerfile ./src  ')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                        }
                }

                roda pipeline
            }
        }

        stage('Deploy no Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }

    }
}