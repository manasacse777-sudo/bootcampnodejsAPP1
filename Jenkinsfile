Jenkins file:

pipeline {
    agent any

    environment {
        IMAGE_NAME = "manasak93/jenksq"
        ACR_NAME = "myynodejsacr777"
        ACR_LOGIN_SERVER = "myynodejsacr777.azurecr.io"
        RESOURCE_GROUP = "myrg-aks"
        AKS_CLUSTER = "menodejsappaks1"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/manasacse777-sudo/bootcampnodejsAPP1.git', branch: 'master'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    script {
                        def scannerHome = tool 'sonar-scanner'
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=bootcampnodejs \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_AUTH_TOKEN
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerlogin',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh "trivy image --skip-version-check $IMAGE_NAME:$IMAGE_TAG"
            }
        }

        stage('ACR Login') {
            steps {
                withCredentials([
                    string(credentialsId: 'AZURE_CLIENT_ID', variable: 'AZURE_CLIENT_ID'),
                    string(credentialsId: 'AZURE_CLIENT_SECRET', variable: 'AZURE_CLIENT_SECRET'),
                    string(credentialsId: 'AZURE_TENANT_ID', variable: 'AZURE_TENANT_ID')
                ]) {
                    sh '''
                    az login --service-principal \
                      -u $AZURE_CLIENT_ID \
                      -p $AZURE_CLIENT_SECRET \
                      --tenant $AZURE_TENANT_ID

                    az acr login --name $ACR_NAME
                    '''
                }
            }
        }

        stage('Tag Image for ACR') {
            steps {
                sh "docker tag $IMAGE_NAME:$IMAGE_TAG $ACR_LOGIN_SERVER/jenksq:$IMAGE_TAG"
            }
        }

        stage('Push to ACR') {
            steps {
                sh "docker push $ACR_LOGIN_SERVER/jenksq:$IMAGE_TAG"
            }
        }

        stage('Connect to AKS') {
            steps {
                withCredentials([
                    string(credentialsId: 'AZURE_CLIENT_ID', variable: 'AZURE_CLIENT_ID'),
                    string(credentialsId: 'AZURE_CLIENT_SECRET', variable: 'AZURE_CLIENT_SECRET'),
                    string(credentialsId: 'AZURE_TENANT_ID', variable: 'AZURE_TENANT_ID')
                ]) {
                    sh '''
                    az login --service-principal \
                      -u $AZURE_CLIENT_ID \
                      -p $AZURE_CLIENT_SECRET \
                      --tenant $AZURE_TENANT_ID

                    az aks get-credentials \
                      --resource-group $RESOURCE_GROUP \
                      --name $AKS_CLUSTER \
                      --overwrite-existing
                    '''
                }
            }
        }

        stage('Deploy to AKS (Helm)') {
            steps {
                sh '''
                helm upgrade --install nodeapp ./helm \
                --set image.repository=$ACR_LOGIN_SERVER/jenksq \
                --set image.tag=$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

