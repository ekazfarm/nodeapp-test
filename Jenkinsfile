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
 
    stage('Deploy ke cluster') {
      steps {
        sshagent(['k8s']) {
          sh "ssh -t -o StrictHostKeyChecking=no root@192.168.14.11"
          sh "scp /var/jenkins_home/workspace/deploy-nodeapp/deploymentservice.yml root@192.168.14.11:/root"
          sh "ssh -t -o StrictHostKeyCheking=no root@192.168.14.11 kubectl delete -f deploymentservice.yml"
          sh "ssh -t -o StrictHostKeyCheking=no root@192.168.14.11 kubectl kubectl apply -f deploymentservice.yml"
        }
      }
    }
 
  } 
 
}