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
            sshagent(['pulse07']) {
            sh "scp -o strictHostkeyChecking=no services.yml node-app-pod.yml pulse07@122.168.182.196:/home/pulse07/"
                 }
        }
        stage('Apply Kubernetes Files') {
      steps {
          withKubeConfig([credentialsId: 'kubeconfig']) {
          sh 'cat deployment.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'
          sh 'kubectl apply -f service.yaml'
          script{
              try{
                  sh "ssh pulse07@"122.168.182.196 kubectl apply -f ."
                  
                }catch(error){
                    sh "ssh pulse07@122.168.182.196 kubectl create -f."
                }
              

                
              }
        }
    }
  
}


def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
