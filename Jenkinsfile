pipeline{
    environment{
        SERVERIP = '52.231.76.110'
        SERVERUSER = 'azureuser'
        SERVERSSH = 'azureuser@52.231.76.110'
        LOCALIMAGE = 'test:latest'
        AZURECR = '$AZURECR'
    }
    agent any
    stages{
        stage('test build'){
            steps{
                sh 'docker build -t $LOCALIMAGE .'
                sh 'echo $SERVERSSH'
            }
        }
        stage('docker push to azurecr'){
            steps {
                script{
                    // 작성해둔 젠킨스 크리덴셜 ID를 넣는다
                    withCredentials([usernamePassword( credentialsId: 'cwleeazurecr', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]){
                        sh "docker login -u $USER -p $PASSWORD $AZURECR"
                        sh "docker tag $LOCALIMAGE $AZURECR/$LOCALIMAGE"
                        sh "docker push $AZURECR/$LOCALIMAGE"
                    }
                }
            }
        }
        stage('ssh deploy') {
            steps{
                sshagent (credentials: ['SSH_private_key']) {
                    sh 'ssh -o StrictHostKeyChecking=no -l $SERVERUSER 52.231.76.110 uname -a'
                    script{
                        withCredentials([usernamePassword( credentialsId: 'cwleeazurecr', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
                            sh 'ssh $SERVERSSH "sudo docker login -u $USER -p $PASSWORD $AZURECR"'
                        }
                    }
                    // sh 'ssh $SERVERSSH "cd cicd_test && git pull"'
                    sh 'ssh $SERVERSSH "sudo docker pull $AZURECR/$LOCALIMAGE"'
                    sh 'ssh $SERVERSSH "sudo docker stop test"'
                    sh 'ssh $SERVERSSH "sudo docker run -p 8000:8000 -d --name test --rm $AZURECR/$LOCALIMAGE"'
                    // sh 'ssh $SERVERSSH "cd cicd_test && sudo docker-compose up --build -d"'
                }
            }
        }
    }
}
