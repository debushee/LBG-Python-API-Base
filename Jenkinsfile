pipeline {
    agent any
    stages {
        stage('Build Flask App') {
            steps {
                sh '''
                docker build -t debushee/flask-lbg-demo:latest -t debushee/flask-lbg-demo:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Build Custom NGINX') {
            steps {
                sh '''
                cd ./nginx
                docker build -t debushee/nginx-lbg-demo:latest -t debushee/nginx-lbg-demo:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Push Images') {
            steps {
                sh '''
                docker push debushee/flask-lbg-demo:latest
                docker push debushee/flask-lbg-demo:build-$BUILD_NUMBER
                docker push debushee/nginx-lbg-demo:latest
                docker push debushee/nginx-lbg-demo:build-$BUILD_NUMBER
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
