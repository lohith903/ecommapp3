pipeline {
    agent any

    environment {
        SSH_KEY = credentials('f2825fcd-e630-43da-ae1b-f0ebf49cf13e')
    }

    stages {
        stage('Build Project') {
            steps {
                bat "mvn clean install"
            }
        }

        stage('Deploy WAR File') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: '8a55417d-06a3-4b2d-bda0-1c84f34cf805', keyFileVariable: 'SSH_KEY')]) {
                    script {
                    

                        // Use scp to copy the WAR file to the remote server
                        bat "ssh -o StrictHostKeyChecking=no -i %SSH_KEY%   ec2-user@35.175.151.190 'mvn -v'"
                      //  bat "ssh -i  %SSH_KEY% ec2-user@54.82.125.173 '/opt/tomcat/apache-tomcat-9.0.84/bin/startup.sh'"
                    }
                }
            }
        }

      /*  stage('Start Application') {
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
