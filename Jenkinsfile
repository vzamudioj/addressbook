pipeline{
    agent any
    tools {
        maven 'maven'
    }
    environment{
        cred = credentials('aws-key')
    }

    stages{
        stage('Checkout out code'){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/vzamudioj/addressbook.git']])
            }
        }
         stage('SonarQube Analysis') {
            steps{
                script {
                    def mvn = tool 'maven';
                    withSonarQubeEnv(installationName: 'sonarqube-server') {
                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=devops-working -Dsonar.projectName='devops-working'"
                    }
                }
            }
             
         }
         stage('Quality Gate'){
             steps{
                 script {
                     timeout(time: 10, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                        error "SonarQube Quality Gate failed with status: ${qg.status}"
                        }
                    }
                 sh 'echo hi everone'    
             
                }
             }
         }
         stage('Docker build'){
            steps{
                sh 'docker -v'
                sh 'docker build -t 881630945677.dkr.ecr.us-east-1.amazonaws.com/vicontainers:${BUILD_NUMBER} .'
            }
        }
        stage('Docker push to ECR'){
            steps{
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 881630945677.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 881630945677.dkr.ecr.us-east-1.amazonaws.com/vicontainers:${BUILD_NUMBER}'
                
            }
        }
         
    }
}
