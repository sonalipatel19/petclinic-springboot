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
                bat 'curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin'
            }
        }
        stage('File Scanning by Trivy') {
            steps {
                echo "This is Trivy Scanning stage"
                bat "trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL ."
            }
        }
    }
}