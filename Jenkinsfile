pipeline{
    agent any
    stages{
        stage("Git Checkout"){
            steps{
                git branch: 'devops', url: 'https://github.com/anuragjos/demo-counter-app.git'
            }
        }
    }
}