pipeline{
    agent any
    stages{
        stage('Checkout out code'){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/vzamudioj/TestSonarqubeRepoCode.git']])
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
                 sh 'echo hi'    
             
                }
             }
         }
         
    }
}
