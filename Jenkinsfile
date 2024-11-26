pipeline {

    agent any

    environment {
        SERVER_IP = credentials('PROD-SERVER-IP')
    }

    stages {

        stage('Setup') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage ('Test'){
            steps {
                sh 'pytest'
            }
        }

        stage ('Package Code') {
            steps {
                sh "zip -r myapp.zip ./* -X '*.git*'"
                sh 'ls -lrta'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'sshkey-ec2user', keyFileVariable: 'KEY', usernameVariable: 'USERNAME')]) {

                 sh  "ssh -i ${KEY} ${USERNAME}@${SERVER_IP} 'ls -lrt'"

                }
            }
        }

    }




}