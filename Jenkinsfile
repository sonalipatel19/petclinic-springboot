pipeline {
    agent any
    tools {
      maven 'maven'
    }
    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'master', url: 'https://github.com/sonalipatel19/petclinic-springboot.git'
            }
        }
        stage('Maven Compile') {
            steps {
                echo "This is Maven Compile stage"
                bat "mvn compile"
            }
        }
        stage('Maven Test') {
            steps {
                echo "This is Maven Test stage"
                bat "mvn test"
            }
        }
        stage('Install Trivy') {
            steps {
                bat '''
                curl -L -o trivy.zip https://github.com/aquasecurity/trivy/releases/latest/download/trivy_0.50.1_Windows-64bit.zip
                powershell -Command "Expand-Archive -Force trivy.zip ."
                '''
            }
        }
        stage('File Scanning by Trivy') {
            steps {
                echo "This is Trivy Scanning stage"
                bat "trivy.exe fs --format table --output trivy-report.txt --severity HIGH,CRITICAL ."
            }
        }
    }
}