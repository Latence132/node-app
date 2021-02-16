pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
        IMAGE_URL_WITH_TAG = "latence132/node-app:${DOCKER_TAG}"
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t ${IMAGE_URL_WITH_TAG}"
            }
        }
        stage('Push Docker Image'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'dockerhubpass')]) {
                    sh "docker login -u latence132 -p ${dockerhubpass}"
                    sh "docker push ${IMAGE_URL_WITH_TAG}"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['kops']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@35.180.129.197:/home/ec2-user/"
                script{
                    try{
                        sh "ssh ec2-user@35.180.129.197 kubctl apply -f ."
                    }catch(error){
                        sh "ssh ec2-user@35.180.129.197 kubctl create -f ."
                    }
                }

                }
        }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
