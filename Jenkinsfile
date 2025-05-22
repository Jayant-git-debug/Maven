pipeline{
    agent any
    tools{
        maven "Maven-3.9.8"
        jdk 'Java-17'
    }

    environment{
        ROLE = "Developer"
        DOCKER_VERSION = "v1"
    }
    stages{
        stage('Clean and Build the code'){
            steps{
                sh '''
                mvn --version
                mvn compile
                mvn clean package
                '''
            }
        }
        stage('Building the docker Image and Run Container'){
            steps{
                sh '''
                docker build -t jayant700/maven-docker-app03:$DOCKER_VERSION .
                docker container run --name project22 -d jayant700/maven-docker-app03:$DOCKER_VERSION
                '''
            }

        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: '9c870bf9-76b9-49b2-8ae3-9bbaeac4a981', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push jayant700/maven-docker-app02:$DOCKER_VERSION
                    '''
                }
            }
        

        }
         stage("Delet all container"){
            steps{
                sh 'docker stop $(docker ps -q) && docker rm $(docker ps -aq)'
            }

        }



    }


}
