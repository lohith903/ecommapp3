pipeline {
  agent any
  environment {
    SSH_KEY = credentials('c1aa46bd-7622-414f-8c26-c4579e245a34')
    PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
  }
  stages {
    stage('Build Project') {
      steps {
        sh 'mvn clean install'
      }
    } 
    // Transfer
    stage('Deploy to Remote Server') {
      steps {
        script {
          def remoteServer = '54.158.93.160'
          def remoteDirectory = '/temp/'
          def warFileName = 'EcommerceApp.war'
          sshPublisher(
            publishers: [
              sshPublisherDesc(
                configName: 'targetr', // The SSH configuration name from Jenkins
                transfers: [
                  sshTransfer(
                    sourceFiles: "target/${warFileName}",
                    removePrefix: 'target',
                    remoteDirectory: "${remoteDirectory}"
                  )
                ]
              )
            ]
          )
        }
      }
    }

    
       stage('Deploy WAR File') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'c1aa46bd-7622-414f-8c26-c4579e245a34', keyFileVariable: 'SSH_KEY')]) {
          script {

           
            sh 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@54.158.93.160 "bash /home/ec2-user/commamd.txt"'
         
          }
        }
      }
    }
  }
  post {
    success {
      echo 'Pipeline succeeded!'
      // Any cleanup or additional steps you want to perform on success (t1)
    }
    failure {
      echo 'Pipeline failed. Check the console output for details.'
      // Any cleanup or additional steps you want to perform on failure
    }
  }
}
