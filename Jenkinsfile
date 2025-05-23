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
                docker build -t jayant700/maven-docker-app17 .
                docker container run --name project17 -dit jayant700/maven-docker-app17
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
                        docker push jayant700/maven-docker-app17:$DOCKER_VERSION
                    '''
                }
            }
        

        }
        stage('Create the Nodes'){
            steps {
               sh '''
               eksctl create nodegroup --cluster=labeks-cluster-jenkin --region=ap-south-1 --name=eksdemo1-ng-public1 --node-type=t3.small --nodes=1 --nodes-min=1 --nodes-max=2 --node-volume-size=20 --ssh-access --ssh-public-key=Jenkin --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access
               '''
            }
        }

        stage('Deployment of the code'){
            steps {
                sh 'kubectl apply -f  EKS-DEPLOY-APP.yml'
            }
        }
        stage("Delete Container"){
             steps {
                 sh '''
                 docker stop project17
                 docker rm project17
                 '''
            }
        }

    }

}



