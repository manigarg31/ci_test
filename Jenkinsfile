pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3"
        dockerTool "docker"

    }

    stages {
        stage('VCS') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/manigarg/ci_test.git'

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withDockerRegistry(url: 'https://172.20.10.5:5000') {

                        // Build and Push
                        def webAppImage = docker.build("manigarg/hello-world-webapp:latest");
                        webAppImage.push();
                    }
                }
            }
        }
        stage('response') {
            steps {
                script {
                    def response = httpRequest 'http://localhost:8080/jenkins/api/json?pretty=true'
                    println("Status: "+response.status)
                    println("Content: "+response.content)

                }
            }
        }
    }
    post {
        // If Maven was able to run the tests, even if some of the test
        // failed, record the test results and archive the jar file.
        success {
            echo "Success"
        }
        failure {
            echo "Failure! Duh!"
        }
    }
}

