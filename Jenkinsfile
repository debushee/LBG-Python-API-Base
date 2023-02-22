pipeline {
    agent any
    stages {
        stage('Build Flask App') {
            steps {
                sh '''
                docker build -t debushee/python-api-app-kubernetes:latest -t debushee/python-api-app-kubernetes:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Build Custom NGINX') {
            steps {
                sh '''
                cd ./kubernetes/nginx
                docker build -t debushee/nginx-python-api:latest -t debushee/nginx-python-api:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Push Images') {
            steps {
                sh '''
                docker push debushee/python-api-app-kubernetes:latest
                docker push debushee/python-api-app-kubernetes:build-$BUILD_NUMBER
                docker push debushee/nginx-python-api:latest
                docker push debushee/nginx-python-api:build-$BUILD_NUMBER
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                cd ./kubernetes
                kubectl apply -f .
                '''
            }
        }
    }
}
