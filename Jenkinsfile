pipeline {
  agent any

    tools {
    maven 'Maven'
  }

  stages {
    stage('Build Maven') {
      steps {
        script {
          echo "Construindo o projeto maven, aguarde..."
          sh "mvn clean package"
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          echo "Construindo a imagem docker, aguarde..."
          dockerapp = docker.build("ajwfl/meu_app:v${env.BUILD_ID}", '-f Dockerfile .')
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        script {
          echo "Enviando alterações para o DockerHub, aguarde..."
          docker.withRegistry("https://registry.hub.docker.com",'dockerhub'){
                        dockerapp.push("v${env.BUILD_ID}")
                        dockerapp.push("latest")
                    }
                }
            }
        }

    stage('Kubernetes Manifestos') {
      steps {
        script {
          echo "Aplicando manifestos do Kubernetes, aguarde..."
          sh "cd /var/jenkins_home/workspace/AvaliacaoCloudMinsait/k8s && kubectl apply -f web-service.yaml,web-deployment.yaml,mysql-deployment.yaml,mysql-service.yaml"
        }
      }
    }
  }
}