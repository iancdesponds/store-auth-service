pipeline {
    agent any
    
    tools {
        maven 'maven-3'
        jdk 'jdk-21'
    }
    
    environment {
        SERVICE = 'auth'
        NAME = "iancdesponds/${env.SERVICE}"
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
    }
    
    stages {
        stage('Checkout Dependencies') {
            steps {
                script {
                    dir('libs/account') {
                        git branch: 'main', url: 'https://github.com/iancdesponds/store-account.git'
                        sh 'mvn clean install -DskipTests'
                    }
                    
                    dir('libs/auth') {
                        git branch: 'main', url: 'https://github.com/iancdesponds/store-auth.git'
                        sh 'mvn clean install -DskipTests'
                    }
                }
            }
        }
        
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }      
        
        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: env.REGISTRY_CREDENTIALS, 
                                                   usernameVariable: 'USERNAME', 
                                                   passwordVariable: 'TOKEN')]) {
                        sh """
                            docker login -u \$USERNAME -p \$TOKEN
                            docker build -t ${env.NAME}:latest .
                            docker build -t ${env.NAME}:${env.BUILD_NUMBER} .
                            docker push ${env.NAME}:latest
                            docker push ${env.NAME}:${env.BUILD_NUMBER}
                        """
                    }
                }
            }
        }
    }
}