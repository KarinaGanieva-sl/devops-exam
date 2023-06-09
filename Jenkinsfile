pipeline {
    agent any
    tools {
        nodejs '20.3.0'
    }
     environment {
        GLOBAL_ENVIRONMENT = 'NO_DEPLOYMENT'
        ENVIRONMENT_STAGING = 'staging'
        ENVIRONMENT_PRODUCTION = 'production'
    }

    stages {

        stage('Setup environment') {
            steps {
                echo 'Setup environment'
                
                script {      
                    switch (env.BRANCH_NAME) {
                        case 'master':
                            GLOBAL_ENVIRONMENT = 'production'
                            break
                        default: 
                            GLOBAL_ENVIRONMENT = 'staging'
                            break
                    }
                }
            }
        }

        

        stage('Installing dependencies') {
            steps {
                 sh 'npm install express'
            }
        }

        stage('Testing') {
            steps {
                sh 'npm test'
            }
        }

    
       stage('Deploy and run') {
            steps {
                script {
                    if (GLOBAL_ENVIRONMENT == 'staging') {
                        withCredentials([sshUserPrivateKey(credentialsId: 'target-ssh-credentials', keyFileVariable: 'key_file', usernameVariable: 'username')]) {
                    sh 'ssh-keyscan 192.168.105.3 > ~/.ssh/known_hosts'
                    sh 'scp -i ${key_file} server.js ${username}@192.168.105.3:'
                    sh 'ssh -l ${username} -i ${key_file} 192.168.105.3 -C npm install express'
                    sh 'ssh -l ${username} -i ${key_file} 192.168.105.3 -C node server.js'
                    }
                }
                if (GLOBAL_ENVIRONMENT == 'production') {
                        withCredentials([sshUserPrivateKey(credentialsId: 'k8s-ssh-credentials', keyFileVariable: 'key_file', usernameVariable: 'username')]) {
                    sh 'ssh-keyscan 192.168.105.4 > ~/.ssh/known_hosts'
                    sh 'scp -i ${key_file} server.js ${username}@192.168.105.4:'
                    sh 'ssh -l ${username} -i ${key_file} 192.168.105.4 -C npm install express'
                    sh 'ssh -l ${username} -i ${key_file} 192.168.105.4 -C node server.js'
                }
                } 
            }
        }
    }
}
}
