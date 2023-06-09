pipeline {
    agent any
    tools {
        nodejs '20.3.0'
    }
    stages {

        stage('Installing dependencies') {
            steps {
                 sh 'npm install express'
            }
        }

        // stage('Testing') {
        //     steps {
        //         sh 'npm test'
        //     }
        // }

    
       stage('Deploy and run') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'target-ssh-credentials', keyFileVariable: 'key_file', usernameVariable: 'username')]) {
                    sh 'ssh-keyscan 192.168.105.3 > ~/.ssh/known_hosts'
                    sh 'scp -i ${key_file} node index.js ${username}@192.168.105.3:'
                }
            }
        }
    }
}
