pipeline {
    
    agent any

    //env.DOCKER_API_VERSION="1.23"
    
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:30400/"

    try{
        currentBuild.result = "SUCCESS"
        
        stage('Checkout'){
            checkout scm
            sh "git rev-parse --short HEAD > commit-id"
            tag = readFile('commit-id').replace("\n", "").replace("\r", "")
            imageName = "${registryHost}${appName}:${tag}"
            env.BUILDIMG=imageName
        }
        
        stage('build')   {
            sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
        }
    
        stage('Push') {
            sh "docker push ${imageName}"
        }        

        stage('Deploy')  {
            sh "sed 's#127.0.0.1:30400/hello-kenzan:latest#'$BUILDIMG'#' applications/hello-kenzan/k8s/deployment.yaml | kubectl apply -f -"
            sh "kubectl rollout status deployment/hello-kenzan"    
        }
    }

    catch(err)  {
        currentBuild.result = "FAILURE"
    }

}