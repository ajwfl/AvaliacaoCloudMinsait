pipeline {
  agent any

    tools {
    maven 'Maven'
  }

  stages {
    stage('Build Maven') {
      steps {
        script {
          echo "Construindo o projeto maven ..."
          sh "mvn clean package"
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          echo "Construindo a imagem docker ..."
          dockerapp = docker.build("ajwfl/meu_app:v${env.BUILD_ID}", '-f Dockerfile .')
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        script {
          echo "Atualizando o DockerHub ..."
          docker.withRegistry("https://registry.hub.docker.com",'dockerhub'){
                        dockerapp.push("v${env.BUILD_ID}")
                        dockerapp.push("latest")
                    }
                }
            }
        }

    stage('Deploy do Kubernetes') {
      steps {
        script {
          echo "Aplicando manifestos do Kubernetes ..."
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "kubectl apply -f ./k8s/"
            sh "kubectl get all"
        }
        }
      }
    }
  }
}