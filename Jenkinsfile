pipeline{
    agent any
    stages{
        stage("Git Checkout"){
            steps{
                git branch: 'devops', url: 'https://github.com/anuragjos/demo-counter-app.git'
            }
        }
        stage("Unit Testing"){
            steps{
                sh 'mvn test'
            }
        }
        stage("Integration Testing"){
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage("Maven Build"){
            steps{
                sh 'mvn clean install'
            }
        }
        stage("Static Code Analysis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh "mvn clean package sonar:sonar"
                    }
                }
            }
        }
        stage("Sonar Gate Status"){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage("Upload war file to Nexus Repo"){
            steps{
                script{
                    def readPomVersion =  readMavenPom file: 'pom.xml' 
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', file: 'target/Uber.jar', 
                            type: 'jar'
                            ]
                            ], 
                            credentialsId: 'nexus-auth', 
                            groupId: 'com.example', 
                            nexusUrl: '3.110.33.85:8081', 
                            nexusVersion: 'nexus3', 
                            protocol: 'http', 
                            repository: nexusRepo, 
                            version: "${readPomVersion.version}"
                }
            }
        }
        stage("Docker Image Build"){
            steps{
                script{
                    sh '''
                           docker image build -t $JOB_NAME:v1.$BUILD_ID .
                           docker image tag $JOB_NAME:v1.$BUILD_ID anuragjoshi01/$JOB_NAME:v1.$BUILD_ID
                           docker image tag $JOB_NAME:v1.$BUILD_ID anuragjoshi01/$JOB_NAME:latest
                       '''
                }
            }
        }
        stage("Image push to Docker Hub"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker-password')]) {
                        sh 'docker login -u anuragjoshi01 -p ${docker-password}'
                        sh 'docker image push anuragjoshi01/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push anuragjoshi01/$JOB_NAME:latest'
                    }
                }
            }
        }
    }
}