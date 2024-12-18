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
        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }
        stage('Dockerize') {
            steps {
                script {
                    sh '''
                    docker build -t $HARBOR_URL/kft-lab/customers-service ./spring-petclinic-customers-service
                    docker build -t $HARBOR_URL/kft-lab/vets-service ./spring-petclinic-vets-service
                    docker build -t $HARBOR_URL/kft-lab/visits-service ./spring-petclinic-visits-service
                    docker build -t $HARBOR_URL/kft-lab/api-gateway ./spring-petclinic-api-gateway
                    docker build -t $HARBOR_URL/kft-lab/config-server ./spring-petclinic-config-server
                    docker build -t $HARBOR_URL/kft-lab/discovery-server ./spring-petclinic-discovery-server
                    '''
                }
            }
        }
        stage('Push Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'harbor-credentials', usernameVariable: 'HARBOR_USER', passwordVariable: 'HARBOR_PASS')]) {
                    sh '''
                    docker login $HARBOR_URL -u $HARBOR_USERNAME -p $HARBOR_PASSWORD
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
