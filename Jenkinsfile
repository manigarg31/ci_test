pipeline {
    agent any
    environment {
        registry = "manigarg/hello-world-webapp"
        registryCredential = 'dockerhub'
        dockerImage = ''
        }
    stages {
        stage('Initialize'){
            steps {
                script {
                    def dockerHome = tool 'myDocker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    git branch: 'master' ,url: 'https://github.com/manigarg31/ci_test.git'
                    dockerImage = docker.build registry + ":latest"
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }

            post {
                always {
                    sh '''
                    format="\n*->* %s *(%cr) <%cn>*";
                    commit_messages="$(git log -1 --format="$format")";

                    message="${message}\n*JOB_NAME* : ${JOB_NAME} [${BUILD_DISPLAY_NAME}]";
                    message="${message}\n*COMMIT_MESSAGES* : $commit_messages";
                    message="${message}\n***********************************************";
                    echo ${message};
                    
                    curl -X POST https://api.flock.com/hooks/sendMessage/89da56ff-3a24-4e5a-96e8-8f032a1e32c5 -H "Content-Type: application/json" -d "{ 'notification' : '$(getNotification started)', 'text' : '${message}' }";
                    '''
                }
            }
        }
    }
}
