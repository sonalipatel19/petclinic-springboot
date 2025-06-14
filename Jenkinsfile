pipeline {
    agent any
    tools {
      maven 'maven'
    }

    environment {
        IMAGE_NAME = "petclinic"
        IMAGE_TAG = "${BUILD_ID}"
        TENANT_ID = "28b42ab5-7054-448a-868c-9a7f6a33180b"
        ACR_NAME = "petclinicapp"
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        CONTAINER_IMAGE = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        RESOURCE_GROUP = "AKS-RG"
        CLUSTER_NAME = "DEMOAKS"
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
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }  
            }
        }
        stage('Azure Login to ACR') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-secret', 
                        usernameVariable: 'AZURE_USERNAME', 
                        passwordVariable: 'AZURE_PASSWORD'
                    )
                ]) {
                    script {
                        echo 'Azure Login Started'
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
                    echo "Pushing Docker Image to ACR"
                    sh '''
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${CONTAINER_IMAGE}
                    docker push ${CONTAINER_IMAGE}
                    '''
                }  
            }
        }
        stage('Jenkins Login to Kubernetes') {
            steps {
               withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-secret', 
                        usernameVariable: 'AZURE_USERNAME', 
                        passwordVariable: 'AZURE_PASSWORD'
                    )
                ]) {
                    script {
                        sh '''
                        echo 'Jenkins Login to Azure and Kubernetes'
                        az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                        az aks get-credentials --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME --overwrite-existing
                        '''
                    }
                } 
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploy to Kubernetes'
                    sh '''
                    kubectl delete deploy springboot
                    kubectl apply -f k8s/springboot-deployment.yaml
                    echo 'Deployed to Kubernetes'
                    '''
                }
            }
        }

    }
}