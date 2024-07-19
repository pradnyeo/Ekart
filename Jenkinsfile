pipeline {
    agent any
    
    tools {
        maven 'maven'
    }
    stages{
        stage('git-checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/pradnyeo/Ekart.git'
            }    
        }
        stage('compile') {
            steps {
                sh 'mvn clean compile'
            }    
        }
        stage('build') {
            steps {
                sh 'mvn -DskipTests=True package'
            }    
        }
        stage('PushToNexus') {
            steps {
                configFileProvider([configFile(fileId: '5dd39c28-e1b9-4a24-89c7-4eade5528d4c', variable: 'mavensettings')]) {
                    
                    sh 'mvn -s $mavensettings deploy -DskipTests=true'
                  }
            }
        }
        stage('Build_Image') {
            steps {
                sh 'docker build -t shopping-kart -f docker/Dockerfile .'
                sh 'docker tag shopping-kart pradnyeo/shopping-kart:latest'
            }
        }
        stage('Push_Image') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh 'docker login -u pradnyeo -p ${DockerHubPwd}'
                 }
                 sh 'docker push pradnyeo/shopping-kart:latest'
            }
        }
        stage('Run_container') {
            steps {
                sh 'docker run -d --name Ekart1 -p 8070:8070 pradnyeo/shopping-kart'
            }
        }
        
    }
}
