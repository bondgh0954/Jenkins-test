pipeline {

    agent any
    tools{
        maven 'maven'
    }

    stages{

        stage("increment version"){
            steps{
                script{
                    echo 'incrementing app version....'
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"

                    
                }
            }
        }

        

      

        stage("build jar"){
            steps{
                script{
                    echo 'building application jar file ...........'
                    sh 'mvn clean package'
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

        stage('commit changes'){
            steps{
                script{
                    echo "commiting changes back to the git repo..."
                    withCredentials([usernamePassword(credentialsId: "github-credentials", usernameVariable: "USER", passwordVariable: "PASS")]){
                        
                        sh 'git config user.email "jenkins@example.com"'
                        sh 'git config user.name "jenkins"'
                        sh 'git status'
                        sh 'git branch'
                        sh 'git config --list'
                        sh "git remote set-url origin https://${USER}:${PASS}@github.com/bondgh0954/Jenkins-test.git"
                        sh 'git add .'
                        sh 'git commit -m "commit version increment update"'
                        sh 'git push origin HEAD:main'
                    }
                     
                }
            }
        }
    }
}