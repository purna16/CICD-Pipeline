pipeline {
    agent any

    stages {
        stage('git checkout') {
            steps {
                script{
                    checkout scmGit(branches: [[name: '*/main']], extengitsions: [], userRemoteConfigs: [[url: 'https://github.com/purna16/CICD-Pipeline.git']])
                }
        
            }
        }
        stage('unit tetsing'){
            steps{
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('integration'){
            steps{
                script{
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('mvn build'){
            steps{
                script{
                    sh 'mvn clean install'
                }
            }
        }
        stage('static code analysis'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonarqube') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }
        stage('soar quality gate'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
                }
            }
        }
        stage{
            steps{
                script{
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/springboot-1.0.0.jar', 
                            type: 'jar'
                            ]
                    ],
                     credentialsId: 'nexus', 
                     groupId: 'com.example', 
                     nexusUrl: '34.229.14.39:8081', 
                     nexusVersion: 'nexus3', 
                     protocol: 'http', 
                     repository: 'http://34.229.14.39:8081/repository/purnacicd-release/', 
                     version: '1.0.0'
                }
            }
        }
    }
}
