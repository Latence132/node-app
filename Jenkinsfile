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
        // stage('Docker Deploy Dev'){
        //     steps{
        //         sshagent(['tomcat-dev']) {
        //             withCredentials([string(credentialsId: 'nexus-pwd', variable: 'nexusPwd')]) {
        //                 sh "ssh ec2-user@172.31.0.38 docker login -u admin -p ${nexusPwd} ${NEXUS_URL}"
        //             }
		// 			// Remove existing container, if container name does not exists still proceed with the build
		// 			sh script: "ssh ec2-user@172.31.0.38 docker rm -f nodeapp",  returnStatus: true
                    
        //             sh "ssh ec2-user@172.31.0.38 docker run -d -p 8080:8080 --name nodeapp ${IMAGE_URL_WITH_TAG}"
        //         }
        //     }
        // }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
