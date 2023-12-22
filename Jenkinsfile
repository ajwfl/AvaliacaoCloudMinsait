pipeline {
  agent any

  tools {
    maven 'Maven'
  }

  stages {
    stage('Build') {
      steps {
        script {
          echo "Construindo o projeto maven ! "
          sh 'mvn clean package'
        }
      }
    }

    stage('Testes unitarios usando o Maven ') {
      steps {
        script {
          echo "Executando testes unitários ! "
          sh 'mvn test'
        }
      }
    }

    stage('Build Image') {
      steps {
        script {
          echo "Construindo a imagem docker ! "
          sh 'docker build -t ajwfl/imagemavaliacao:latest .'
          dockerapp = docker.build("ajwfl/imagemavaliacao :v${env.BUILD_ID}", '-f ./Dockerfile .')
        }
      }
    }

    stage('Push Image DockerHub') {
      steps {
        script {
          echo "Enviando novas alterações para o DockerHub ! "


          withCredentials([
            usernamePassword(credentialsId: 'docker-credential', passwordVariable: 'passwd', usernameVariable: 'user'
          )]) {
            sh "docker login -u ${env.user} -p ${env.passwd}"
            sh "docker push ajwfl/imagemavaliacao:latest"
          }
        }
      }
    }
    stage('Deploy no Kubernetes') {
      steps {
           withKubeConfig([credentialsId: 'kubeconfig']){
                echo "Aplicando manifestos do Kubernetes ! "
                sh 'kubectl apply -f ./k8s/web-deployment.yaml'
            }    
            withKubeConfig([credentialsId: 'kubeconfig']){
                echo "Aplicando manifestos do Kubernetes ! "
                sh 'kubectl apply -f ./k8s/mysql-deployment.yaml'
            }
      }
    } 
  }
}