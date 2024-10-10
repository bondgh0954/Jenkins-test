pipeline {

    agent any
    tools{
        maven 'maven'
    }

    stages{

        stage("increment version"){
            steps{
                script{
                    echo 'incrementing version of application...'
                    sh 'mvn build-helper:parse-version versions:set \
                       -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                       versions:commit'
                    def matcher = readfile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }

        

      

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
                    sh "docker build -t nanaot/java-app:$IMAGE_NAME ."
                    echo ' logging in to docker private registry......'
                    withCredentials([usernamePassword(credentialsId: "docker-credentials", passwordVariable: "PASS", usernameVariable: "USER")]){
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push nanaot/java-app:$IMAGE_NAME"
                    }
                }
            }
        }
    }
}