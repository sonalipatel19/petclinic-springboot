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
        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.organization="devproj1" \
                    -Dsonar.projectName="PetClinic" \
                    -Dsonar.projectKey="devproj1_petclinic" \
                    -Dsonar.java.binaries=. \
                    -Dsonar.exclusions=**/trivy-report.txt 
                    '''
                }
            }
        }
        
    }
}