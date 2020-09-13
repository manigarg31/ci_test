pipeline {
    agent any
    environment {
        registry = "manigarg/hello-world-webapp"
        registryCredential = 'dockerhub'
        dockerImage = ''
        GIT_BRANCH = 'mani'
        }
  stages {
    stage('Cloning Git') {
      steps {
        sh 'git checkout ${GIT_BRANCH}'
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
      post {
        always {
            echo """{"project_name": "$JOB_NAME", "build_commit": "$env.GIT_COMMIT", "build_number": "$env.BUILD_NUMBER",  "status": "FAILURE"}"""
        }
        success {
            httpRequest customHeaders: [[maskValue: false, name: 'Content-Type', value: 'application/json']], httpMode: 'POST', requestBody: "{'project_name': '$JOB_NAME', 'build_commit': '$env.GIT_COMMIT', 'build_number': '$env.BUILD_NUMBER',  'status': 'SUCCESS'}", responseHandle: 'NONE', url: 'https://api.flock.com/hooks/sendMessage/89da56ff-3a24-4e5a-96e8-8f032a1e32c5', wrapAsMultipart: false
        }
        failure {
            httpRequest customHeaders: [[maskValue: false, name: 'Content-Type', value: 'application/json']], httpMode: 'POST', requestBody: "{'project_name': '$JOB_NAME', 'build_commit': '$env.GIT_COMMIT', 'build_number': '$env.BUILD_NUMBER',  'status': 'FAILED'}", responseHandle: 'NONE', url: 'https://api.flock.com/hooks/sendMessage/89da56ff-3a24-4e5a-96e8-8f032a1e32c5', wrapAsMultipart: false
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



