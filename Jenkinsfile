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

    post {
        always {
            // Chuck Norris aparece em todos os builds
            chuckNorris()
        }

        success {
            echo '🚀 Deploy realizado com sucesso!'
            echo '💪 Chuck Norris aprova seu pipeline DevSecOps!'
            echo "✅ Imagem weslley7/guia-jenkins:${env.BUILD_ID} deployada no Kubernetes"
        }

        failure {
            echo '❌ Build falhou, mas Chuck Norris nunca desiste!'
            echo '🔍 Chuck Norris está investigando o problema...'
            echo '💡 Verifique: Docker build, DockerHub push ou Kubernetes deploy'
        }

        unstable {
            echo '⚠️ Build instável - Chuck Norris está monitorando'
        }
    }
}