pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t rahulkansana/nodeapp:${DOCKER_TAG}"
            }
        }
        stage('dockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerhubpwd')]) {
                    sh "docker login -u admin -p ${admin} ${Docker_hub}"
                    sh "docker push ${dockerhubpwd}"
                    sh "docker push rahulkansana/nodeapp:${DOCKER_TAG}"
                }
            }
        }
        stage{('deploy to k8s')
        steps{
            sh "chmod +x changeTag.sh"
            sh "./changeTag.sh: ${DOCKER_TAG}"
            sh "ssh pulse07@ip "
        }
        stage('Apply Kubernetes Files') {
      steps {
          withKubeConfig([credentialsId: 'kubeconfig']) {
          sh 'cat deployment.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'
          sh 'kubectl apply -f service.yaml'
        }
    }
  
}
                  
        
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
