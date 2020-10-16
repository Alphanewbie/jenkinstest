pipeline{
    agent any
    stages{
        stage('test build'){
            steps{
                sh 'docker build -t test:latest .'
            }
        }
    }
}