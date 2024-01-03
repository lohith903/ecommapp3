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
    
    // Shutdown app
    stage('Shutdown Application') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'c1aa46bd-7622-414f-8c26-c4579e245a34', keyFileVariable: 'SSH_KEY')]) {
          script {
            // Shutdown Tomcat
            def exitCode1 = sh(script: 'sudo ssh -i $SSH_KEY ec2-user@54.82.125.173 "/opt/tomcat/apache-tomcat-9.0.84/bin/shutdown.sh"', returnStatus: true)
            if (exitCode1 == 0) {
              echo 'Tomcat shutdown successfully.'
            } else {
              error "Failed to shutdown Tomcat. Exit code: ${exitCode1}"
            }

            // Verify Maven version on the remote server
            def exitCode2 = sh(script: 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "mvn -v"', returnStatus: true)
            if (exitCode2 == 0) {
              echo 'Maven version verified on the remote server.'
            } else {
              error "Failed to verify Maven version. Exit code: ${exitCode2}"
            }

            // Deploy the WAR file to Tomcat webapps directory
            def exitCode3 = sh(script: 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "sudo mv /home/ec2-user/temp/EcommerceApp.war /opt/apache-tomcat-9.0.84/webapps"', returnStatus: true)
            if (exitCode3 == 0) {
              echo 'WAR file deployed successfully.'
            } else {
              error "Failed to deploy WAR file. Exit code: ${exitCode3}"
            }

            // Start Tomcat
            def exitCode4 = sh(script: 'sudo ssh -i $SSH_KEY ec2-user@54.82.125.173 "/opt/tomcat/apache-tomcat-9.0.84/bin/startup.sh"', returnStatus: true)
            if (exitCode4 == 0) {
              echo 'Tomcat started successfully.'
            } else {
              error "Failed to start Tomcat. Exit code: ${exitCode4}"
            }

            // Verify Maven version again (optional)
            def exitCode5 = sh(script: 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "mvn -v"', returnStatus: true)
            if (exitCode5 == 0) {
              echo 'Maven version verified on the remote server again.'
            } else {
              error "Failed to verify Maven version again. Exit code: ${exitCode5}"
            }
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
