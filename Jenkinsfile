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
          sh 'docker build -t ajwfl/imagemAvaliacao:latest .'
#         dockerapp = docker.build("ajwfl/imagemavaliacao :v${env.BUILD_ID}", '-f ./Dockerfile .')
        }
      }
    }

    stage('Push DockerHub') {
      steps {
        script {
          echo "Enviando novas alterações para o DockerHub ! "


          withCredentials([
            usernamePassword(credentialsId: 'docker-credential', passwordVariable: 'passwd', usernameVariable: 'user'
          )]) {
            sh "docker login -u ${env.user} -p ${env.passwd}"
            sh 'docker push ajwfl/imagemavaliacao:latest'
          }



# 	  docker.withRegistry('https://registry.hub.docker.com','dockerhub'){
#         dockerapp.push('latest')
#         dockerapp.push("v${env.BUILD_ID}")



        }
      }
    }

    stage('Deploy no Kubernetes') {
      steps {
        script {
          echo "Aplicando manifestos do Kubernetes ! "


          sh 'kubectl apply -f k8s/'
          sh 'kubectl get all'

#       withKubeConfig([credentialsId: 'kubeconfig']){
#            // sh "echo 'passou' "
#            sh "cd /var/jenkins_home/workspace/AvaliacaoCloudMinsait/k8s && kubectl apply -f web-service.yaml,api-deployment.yaml,db-deployment.yaml,db-service.yaml"
#            // sh "kubectl set image deployment/web web=matheusmprado/sampletodoaula3:latest"
        }

#   stage ("Verifica Kubernetes"){
#       steps{
#           withKubeConfig([credentialsId: 'kubeconfig']){
#               sh "kubectl get all"
#           }
#       }
#   } 
  }
}

