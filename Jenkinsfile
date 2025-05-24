pipeline{
    agent any
    tools{
        maven "Maven-3.9.8"
        jdk 'Java-17'
    }

    environment{
        ROLE = "Developer"
        DOCKER_VERSION = "latest"
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
                docker build -t jayant700/maven-docker-app77 .
                docker container run --name project77 -dit jayant700/maven-docker-app77
                docker stop project77
                docker rm project77
                '''
            }

        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: '8354bed1-0335-48c9-aa2c-4c7f8d7b87d0', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push jayant700/maven-docker-app77:$DOCKER_VERSION
                    '''
                }
            }
        

        }
        stage('Create the Nodes'){
            steps {
               sh '''
               eksctl create nodegroup --cluster=labekscluster --region=ap-south-1 --name=eksdemo1-ng-public1 --node-type=t3.small --nodes=1 --nodes-min=1 --nodes-max=2 --node-volume-size=20 --ssh-access --ssh-public-key=Jenkin --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access
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



