pipeline {
    agent any
    stages {
        stage('Build Flask App') {
            steps {
                sh '''
                docker build -t eu.gcr.io/lbg-cloud-incubation/niall-python-api-app-kubernetes:latest -t eu.gcr.io/lbg-cloud-incubation/niall-python-api-app-kubernetes:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Build Custom NGINX') {
            steps {
                sh '''
                cd ./kubernetes/nginx
                docker build -t eu.gcr.io/lbg-cloud-incubation/niall-nginx-python-api:latest -t eu.gcr.io/lbg-cloud-incubation/niall-nginx-python-api:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Push Images') {
            steps {
                sh '''
                docker push eu.gcr.io/lbg-cloud-incubation/niall-python-api-app-kubernetes:latest
                docker push eu.gcr.io/lbg-cloud-incubation/niall-python-api-app-kubernetes:build-$BUILD_NUMBER
                docker push eu.gcr.io/lbg-cloud-incubation/niall-nginx-python-api:latest
                docker push eu.gcr.io/lbg-cloud-incubation/niall-nginx-python-api:build-$BUILD_NUMBER
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                cd ./kubernetes
                kubectl apply -f .
                cd ./nginx
                kubectl apply -f .
                kubectl rollout restart deployment python-api-app
                kubectl rollout restart deployment nginx
                '''
            }
        }
    }
}
