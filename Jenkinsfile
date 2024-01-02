pipeline {
  agent any


  stages {
    /*stage('Build Project') {
      steps {
        sh "mvn clean install"
      }
    } */

    stage('Deploy WAR File') {
      steps {
      sh "ssh -o StrictHostKeyChecking=no -i "/home/ec2-user/rut102.pem"ec2-user@3.91.230.20 'mvn -v'"
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
      // Any cleanup or additional steps you want to perform on success (t1)
    }
    failure {
      echo 'Pipeline failed. Check the console output for details.'
      // Any cleanup or additional steps you want to perform on failure
    }
  }
}
