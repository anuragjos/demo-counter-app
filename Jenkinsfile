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
    }
}