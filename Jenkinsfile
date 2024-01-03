pipeline {
    agent any
    stages {
        stage('Build Project') {
            steps {
                // Your build steps to create the WAR file
                sh 'mvn clean install'
            }
        }
        stage('Deploy to Remote Server') {
            steps {
                script {
                    def remoteServer = 'your_remote_server'
                    def remoteDirectory = '/path/to/remote/directory'
                    def warFileName = 'your-app.war'
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'your_ssh_configuration', // The SSH configuration name from Jenkins
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

     stage('custom commands') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'c1aa46bd-7622-414f-8c26-c4579e245a34', keyFileVariable: 'SSH_KEY')]) {
          script {
           
            
            sh 'ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@44.211.82.24 "bash /home/ec2-user/command.txt"'
             
           
                      
          
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
