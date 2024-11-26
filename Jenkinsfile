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
                sh  ''' 
                    scp -i -o ${KEY} -o StrictHostKeyChecking=no myapp.zip ${USERNAME}@${SERVER_IP}:/home/${USERNAME}/
                    ssh -i ${KEY} -o StrictHostKeyChecking=no ${USERNAME}@${SERVER_IP} <<< EOF
                    ls -lrt
                    unzip -d myapp.zip -d /home/${USERNAME}/app/
                    source /home/${USERNAME}/app/venv/bin/activate
                    cd /home/${USERNAME}/app/
                    pip install -r requirements.txt
                    sudo systemctl restart flaskapp.service
EOF
                '''

                }
            }
        }

    }




}