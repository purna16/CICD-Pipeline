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
        stage('nexus artifact upload'){
            steps{
                script{
                    def readpomversion= readMavenPom file: 'pom.xml'
                    def nexusRepo = readpomversion.version.endsWith("SNAPSHOT") ? "purnacicd-release-snapshot" : "purnacicd-release"
                   
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/Uber.jar', 
                            type: 'jar'
                            ]
                    ],
                     credentialsId: 'nexus', 
                     groupId: 'com.example', 
                     nexusUrl: '34.229.14.39:8081', 
                     nexusVersion: 'nexus3', 
                     protocol: 'http', 
                     repository: nexusRepo, 
                     version: "${readpomversion.version}"
                }
            }
        }
    }
}
