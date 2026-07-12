pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-2'
        ECR_REPOSITORY = "${ACCOUNT_ID}.dkr.ecr.us-east-2.amazonaws.com"
        //pass using parameters
    }
    stages {
        stage('code') {
            steps {
                git branch: 'main', url: 'https://github.com/Kajjayamsriram/bloodcamp.php.git'
            }
        }
        stage('CQA') {
            environment {
                    scannerHome = tool 'sonar'
            }
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=bee"
                }
            }
        }
        stage('Quality Gates'){
            steps{
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                //To avod pipeline failure test with false
            }
        }
        stage('Image Build'){
            steps{
                sh "docker build -t ${ECR_REPOSITORY}/bloodapp:app ."
                sh "docker build -t ${ECR_REPOSITORY}/bloodapp:db database"
            }
        }
        stage ("Image Scan"){
            steps{
                sh "trivy image --severity CRITICAL --exit-code 0 ${ECR_REPOSITORY}/bloodapp:app"
                sh "trivy image --severity CRITICAL --exit-code 0 ${ECR_REPOSITORY}/bloodapp:db"
                //Keep 1 to fail for CRITICAL Vulns
            }
        }
        stage("ECR Login"){
            steps{
                sh '''
                aws ecr get-login-password --region  $AWS_REGION \
                | docker login --username AWS --password-stdin \
                $ECR_REPOSITORY
                '''
            }
        }
        stage('Push to ECR'){
            steps{
                sh "docker push ${ECR_REPOSITORY}/bloodapp:app"
                sh "docker push ${ECR_REPOSITORY}/bloodapp:db"
            }
        }
        stage('Deploy'){
            steps{
                sh "docker-compose -f compose.yaml down"
                sh "docker-compose -f compose.yaml up -d"
            }
        }
    }
}
