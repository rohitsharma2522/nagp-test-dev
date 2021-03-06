pipeline{
    agent any
    tools {
        maven "Maven3"
    }
    environment {
        registry = "rohit2522/nagp-jenkins-assignment"
    }
    stages{
        stage("Checkout"){
            steps {
                echo "checkout master branch"
                checkout scm
            }
        }
        stage("Build") {
            steps {
                echo "build master"
                bat "mvn clean install"
            }
        }
        stage("Test") {
            steps {
                echo "test cases"
                bat "mvn test"
            }
        }
        stage("Sonar Analysis") {
            steps {
                withSonarQubeEnv('Test_Sonar') {
                    bat "mvn sonar:sonar"
                }
               
            }
        }
        stage("Docker image") {
            steps {
                bat "docker build -t i-rohit-2522-master:${BUILD_NUMBER} --no-cache -f Dockerfile ."
            }
        }
        stage("Container") {
            parallel {
                stage("Pre container check"){
                    steps {
                        bat "docker rm -f c-rohit2522-master || exit 0"
                    }
                }
                stage("Push to Docker hub"){
                    steps {
                        bat "docker tag i-rohit-2522-master:${BUILD_NUMBER} ${registry}:master-${BUILD_NUMBER}"
                        bat "docker tag i-rohit-2522-master:${BUILD_NUMBER} ${registry}:master-latest"
                        withDockerRegistry(credentialsId: 'Test_Docker', url:"") {
                        bat "docker push ${registry}:master-${BUILD_NUMBER}"
                        bat "docker push ${registry}:master-latest"
                        }
                    }
                }
                
               
            }
        }
        stage("Docker deployment") {
            steps {
                bat "docker run --name c-rohit2522-master -d -p 7200:8080 ${registry}:master-latest"
            }
        }
        stage("GK8S") {
            steps {
                bat "kubectl apply -f deployment.yaml"
            }
        }
    }
}