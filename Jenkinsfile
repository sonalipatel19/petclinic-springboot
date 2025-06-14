pipeline {
    agent any
    tools {
      maven 'maven'
    }

    environment {
        IMAGE_NAME = "petclinic"
        IMAGE_TAG = "${BUILD_ID}"
        ACR_NAME = "petclinicapp"
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        CONTAINER_IMAGE = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'master', url: 'https://github.com/sonalipatel19/petclinic-springboot.git'
            }
        }
        
        stage('Maven Package') {
            steps {
                echo "This is Maven Package stage"
                sh "mvn package"
            }
        }
        stage('Docker Build') {
            steps {
                script{
                    echo "Docker Build Started"
                    docker.build ("$IMAGE_NAME:$IMAGE_TAG")
                }  
            }
        }
        stage('Azure Login to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-secret', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                    script {
                        echo 'Azure Login'
                        sh '''
                        az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                        az acr login --name $ACR_NAME
                        '''                    
                    }
                }
            }
        }
        stage('Docker Push') {
            steps {
                script{
                    echo "Docker Push Started"
                    sh '''
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${CONTAINER_IMAGE}
                    docker push ${CONTAINER_IMAGE}
                    '''
                }  
            }
        }
    }
}