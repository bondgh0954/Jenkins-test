pipeline {

    agent any
    tools{
        maven 'maven'
    }

    stages{

        

      

        stage("build jar"){
            steps{
                script{
                    echo 'building application jar file ...........'
                    sh 'mvn package'
                }
            }
        }
        stage("building docker image"){
            steps{
                script{
                    echo 'building application into docker image........'
                    sh "docker build -t nanaot/java-app:55 ."
                    echo ' logging in to docker private registry......'
                    withCredentials([usernamePassword(credentialsId: "docker-credentials", passwordVariable: "PASS", usernameVariable: "USER")]){
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push nanaot/java-app:55"
                    }
                }
            }
        }
    }
}