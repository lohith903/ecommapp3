pipeline {
    agent any
    
    environment {
        SSH_KEY = credentials('c1aa46bd-7622-414f-8c26-c4579e245a34')
        PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
        REMOTE_SERVER = '54.82.125.173'
        REMOTE_DIRECTORY = '/your/remote/directory/' // Specify the actual directory path on the remote server
        WAR_FILE_NAME = 'EcommerceApp.war'
        TOMCAT_BIN_PATH = '/opt/tomcat/apache-tomcat-9.0.84/bin/'
    }

    stages {
        stage('Build Project') {
            steps {
                script {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Transfer to Remote Server') {
            steps {
                script {
                    // Transfer the WAR file to the remote server
                    sshPublisher(
                        continueOnError: true,
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'targetr', // The SSH configuration name from Jenkins
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: "target/${WAR_FILE_NAME}",
                                        removePrefix: 'target/',
                                        remoteDirectory: "${REMOTE_DIRECTORY}"
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Assuming Tomcat is already installed and configured on the remote server
                    sh "ssh -i ${SSH_KEY} ${REMOTE_SERVER} ${TOMCAT_BIN_PATH}/shutdown.sh && sleep 5 && ${TOMCAT_BIN_PATH}/startup.sh"
                }
            }
        }
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
