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
        stage('Push Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'harbor-credentials', usernameVariable: 'HARBOR_USER', passwordVariable: 'HARBOR_PASS')]) {
                    sh '''
                    docker login $HARBOR_URL -u $HARBOR_USERNAME -p $HARBOR_PASSWORD
                    
                    # Push the docker images to Harbor
                    docker push $HARBOR_URL/kft-lab/customers-service
                    docker push $HARBOR_URL/kft-lab/vets-service
                    docker push $HARBOR_URL/kft-lab/visits-service
                    docker push $HARBOR_URL/kft-lab/api-gateway
                    docker push $HARBOR_URL/kft-lab/config-server
                    docker push $HARBOR_URL/kft-lab/discovery-server
                    '''
                }
            }
        }
    }
}
