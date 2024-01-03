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
// transfer
        stage('Deploy to Remote Server') {
            steps {
                script {
                    def remoteServer = '44.211.82.24'
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

//end transfer
// shutdown app
    stage('shutdown Application') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'c1aa46bd-7622-414f-8c26-c4579e245a34', keyFileVariable: 'SSH_KEY')]) {
          // Use SSH to run the command to start the application
          def exitCode1 =  sh'sudo ssh -i %SSH_KEY% ec2-user@54.82.125.173 "/opt/tomcat/apache-tomcat-9.0.84/bin/shutdown.sh"'
           if (exitCode1 == 0) {
                        echo "Custom command started successfully."
                    } else {
                        error "Failed to start the custom command. Exit code: ${exitCode1}"
                    }
          def exitCode2 = sh 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "mvn -v"'
           if (exitCode2 == 0) {
                        echo "Custom command started successfully."
                    } else {
                        error "Failed to start the custom command. Exit code: ${exitCode2}"
                    }
          def exitCode3 = sh 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "sudo mv /home/ec2-user/temp/EcommerceApp.war /opt/apache-tomcat-9.0.84/webapps"'
           if (exitCode3 == 0) {
                        echo "Custom command started successfully."
                    } else {
                        error "Failed to start the custom command. Exit code: ${exitCode3}"
                    }
          def exitCode4 = sh'sudo ssh -i %$SSH_KEY% ec2-user@54.82.125.173 "/opt/tomcat/apache-tomcat-9.0.84/bin/startup.sh"'
           if (exitCode4 == 0) {
                        echo "Custom command started successfully."
                    } else {
                        error "Failed to start the custom command. Exit code: ${exitCode4}"
                    }
          def exitCode5 = sh 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "mvn -v"'
           if (exitCode5 == 0) {
                        echo "Custom command started successfully."
                    } else {
                        error "Failed to start the custom command. Exit code: ${exitCode5}"
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
