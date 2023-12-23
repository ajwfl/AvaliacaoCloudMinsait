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

    stage('Testes') {
      steps {
        script {
          echo "Executando testes, aguarde..."
          sh 'mvn test'
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          echo "Construindo a imagem docker, aguarde..."
          sh 'docker build -t ajwfl/avaliacao-cloud:latest .'
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        script {
          echo "Enviando alterações para o DockerHub, aguarde..."
          withCredentials([
            usernamePassword(credentialsId: 'docker-credential', passwordVariable: 'passwd', usernameVariable: 'user'
          )]) {
            sh "docker login -u ${env.user} -p ${env.passwd}"
            sh 'docker push ajwfl/avaliacao-cloud:latest'
          }
        }
      }
    }

    stage('Kubernetes Manifestos') {
      steps {
        script {
          echo "Aplicando manifestos do Kubernetes, aguarde..."
          sh 'kubectl apply -f k8s/'
          sh 'kubectl get all'
        }
      }
    }
  }
}