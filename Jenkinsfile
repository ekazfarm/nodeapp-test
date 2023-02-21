pipeline { 
 
  environment { 
    dockerimagename = "ekawafs/nodeapp" 
    dockerImage = "" 
  } 
 
  agent any 
 
  stages { 
 
    stage('Checkout Source') { 
      steps { 
        git 'https://github.com/ekazfarm/nodeapp-test.git' 
      } 
    } 
 
    stage('Build image') { 
      steps{ 
        script { 
          dockerImage = docker.build dockerimagename 
        } 
      } 
    } 
 
    stage('Pushing Image') { 
      environment { 
               registryCredential = 'dockerhublogin' 
           } 
      steps{ 
        script { 
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) { 
            dockerImage.push("latest") 
          } 
        } 
      } 
    } 
 
    stage('Apply Kubernetes Files') {
        steps {
            withKubeConfig([credentialsId: 'kubernetes']) {
            sh 'cat deploymentservice.yaml | sed "s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g" | kubectl apply -f -'
            sh 'kubectl apply -f deploymentservice.yaml'
          }
        }
    }

 
  } 
 
}