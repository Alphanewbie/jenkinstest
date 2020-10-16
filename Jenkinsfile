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
    }
}
