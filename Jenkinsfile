pipeline{
    agent any
    stages{
        stage('test build'){
            steps{
                sh 'docker build -t test:latest .'
            }
        }
        stage('docker push to azurecr'){
            steps {
                script{
                    withCredentials([usernamePassword( credentialsId: 'cwleeazurecr', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]){
                        sh "docker login -u $USER -p $PASSWORD cwleecr.azurecr.io"
                        sh "docker tag test:latest cwleecr.azurecr.io/test:latest"
                        sh "docker push cwleecr.azurecr.io/test:latest"
                    }
                }
            }
        }
        stage('ssh deploy') {
            steps{
                sshagent (credentials: ['SSH_private_key']) {
                    sh 'ssh -o StrictHostKeyChecking=no -l azureuser 52.231.76.110 uname -a'
                    script{
                        withCredentials([usernamePassword( credentialsId: 'cwleeazurecr', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
                            sh 'ssh azureuser@52.231.76.110 "sudo docker login -u $USER -p $PASSWORD cwleecr.azurecr.io"'
                        }
                    }
                    // sh 'ssh azureuser@52.231.76.110 "cd cicd_test && git pull"'
                    sh 'ssh azureuser@52.231.76.110 "sudo docker pull cwleecr.azurecr.io/test:latest"'
                    sh 'ssh azureuser@52.231.76.110 "sudo docker stop test"'
                    sh 'ssh azureuser@52.231.76.110 "sudo docker run -p 8000:8000 -d -name test -rm cwleecr.azurecr.io/test:latest"'
                    // sh 'ssh azureuser@52.231.76.110 "cd cicd_test && sudo docker-compose up --build -d"'
                }
            }
        }
    }
}
