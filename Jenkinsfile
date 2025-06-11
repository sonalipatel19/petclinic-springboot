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
    }
}