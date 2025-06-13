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
        
        stage('Install Trivy') {
            steps {
                bat '''
                curl -L -o trivy.zip https://github.com/aquasecurity/trivy/releases/download/v0.50.1/trivy_0.50.1_Windows-64bit.zip
                powershell -Command "Expand-Archive -Path trivy.zip -DestinationPath . -Force"
                '''
            }
        }
        stage('File Scanning by Trivy') {
            steps {
                echo "This is Trivy Scanning stage"
                bat '''
                .\\trivy.exe fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .
                '''
            }
        }
        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    bat '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.organization="DevOpsProject" \
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