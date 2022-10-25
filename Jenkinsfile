pipeline {
    agent any
    
    tools {
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kdiMbodji/demo-boot.git'
            }
        }
        stage('cleanup workspace') {
            steps {
                sh 'mvn clean'
                
                script {
                    try {
                      sh 'docker stop demo-boot && docker rm demo-boot'  
                    } catch(Exception e) {
                        echo "-=- No image to stop or remove -=- "
                    }
                }
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test Unitaire') {
            steps {
                sh 'mvn test'
            }
            
            post {
                success {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Package') {
            steps {
                echo "-=- Package -=- "
                sh 'mvn package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo "-=- Build Docker Image -=- "
                sh 'docker build -t demo-boot .'
            }
        }
        stage('Deploy to test') {
            steps {
                echo "-=- Deploy to test env in ec2 -=- "
                sh 'docker run -d --name demo-boot -p 8180:8080 demo-boot'
            }
        }
    }
}
