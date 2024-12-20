pipeline {
    agent any
    environment {
        HARBOR_URL = credentials('harbor-registry-url')
        HARBOR_USERNAME = credentials('harbor-credentials-username')
        HARBOR_PASSWORD = credentials('harbor-credentials-password')
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
        stage('Dockerize') {
            steps {
                script {
                    echo 'Building docker images for all Spring PetClinic services...'
                    sh '''
                    # Build all services with the Docker context as the entire project
                    docker build -t $HARBOR_URL/kft-lab/customers-service -f spring-petclinic-customers-service/Dockerfile .
                    docker build -t $HARBOR_URL/kft-lab/vets-service -f spring-petclinic-vets-service/Dockerfile .
                    docker build -t $HARBOR_URL/kft-lab/visits-service -f spring-petclinic-visits-service/Dockerfile .
                    docker build -t $HARBOR_URL/kft-lab/api-gateway -f spring-petclinic-api-gateway/Dockerfile .
                    docker build -t $HARBOR_URL/kft-lab/config-server -f spring-petclinic-config-server/Dockerfile .
                    docker build -t $HARBOR_URL/kft-lab/discovery-server -f spring-petclinic-discovery-server/Dockerfile .
                    '''
                }
            }
        }
        stage('Push Docker Images to Harbor') {
            steps {
                script {
                    echo 'Pushing Docker images to Harbor...'
                    sh """
                        echo '${HARBOR_PASSWORD}' | docker login ${HARBOR_URL} -u ${HARBOR_USERNAME} --password-stdin
                        
                        docker push ${HARBOR_URL}/kft-lab/customers-service
                        docker push ${HARBOR_URL}/kft-lab/vets-service
                        docker push ${HARBOR_URL}/kft-lab/visits-service
                        docker push ${HARBOR_URL}/kft-lab/api-gateway
                        docker push ${HARBOR_URL}/kft-lab/config-server
                        docker push ${HARBOR_URL}/kft-lab/discovery-server
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying Spring PetClinic services using Docker Compose...'
                    sh '''
                    # Login to Harbor to allow Docker Compose to pull images
                    echo '${HARBOR_PASSWORD}' | docker login ${HARBOR_URL} -u ${HARBOR_USERNAME} --password-stdin
                    
                    # Stop and remove any existing containers
                    docker compose down
                    
                    # Pull the latest images
                    docker compose pull
                    
                    # Start the application in detached mode
                    docker compose up -d
                    '''
                }
            }
        }
    }
}
