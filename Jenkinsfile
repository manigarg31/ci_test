pipeline {
    agent any
    environment {
        registry = "manigarg/hello-world-webapp"
        registryCredential = 'dockerhub'
        dockerImage = ''
        }
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/manigarg31/ci_test.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}


