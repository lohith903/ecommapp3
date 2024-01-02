pipeline {
  agent any

  environment {
    SSH_KEY = credentials('f41ebdc7-cd95-4b36-b33d-2662af0fa3c2')
  }

  stages {
    stage('Build Project') {
      steps {
        sh "mvn clean install"
      }
    }

    stage('Deploy WAR File') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'f41ebdc7-cd95-4b36-b33d-2662af0fa3c2', keyFileVariable: 'SSH_KEY')]) {
          script {
           

            // Use scp to copy the WAR file to the remote server
            sh "ssh -o StrictHostKeyChecking=no -i %SSH_KEY% ec2-user@3.91.181.70 'mvn -v'"
           // bat "ssh -i %SSH_KEY% ec2-user@54.82.125.173 '/opt/tomcat/apache-tomcat-9.0.84/bin/startup.sh'"
          }
        }
      }
    }

   /* stage('Start Application') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2user1', keyFileVariable: 'SSH_KEY')]) {
          // Use SSH to run the command to start the application
          bat "ssh -i %SSH_KEY% ec2-user@54.82.125.173 '/opt/tomcat/apache-tomcat-9.0.84/bin/startup.sh'"
        }
      }
    }*/
  }

  post {
    success {
      echo 'Pipeline succeeded!'
      // Any cleanup or additional steps you want to perform on success
    }
    failure {
      echo 'Pipeline failed. Check the console output for details.'
      // Any cleanup or additional steps you want to perform on failure
    }
  }
}
