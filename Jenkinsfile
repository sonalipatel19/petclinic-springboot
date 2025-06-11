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
                sh "mvn compile"
            }
        }
        stage('Maven Test') {
            steps {
                echo "This is Maven Test stage"
                sh "mvn test"
            }
        }
        stage('File Scanning by Trivy') {
            steps {
                echo "This is Trivy Scanning stage"
                sh "trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL ."
            }
        }
    }
}