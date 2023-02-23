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
                script {
                    if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
                        cd ./kubernetes
						sed -e 's,{{namespace}},production,g;' application.yml | kubectl apply -f -
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
                        cd ./kubernetes
						sed -e 's,{{namespace}},development,g;' application.yml | kubectl apply -f -
						'''
					}
                }
                script {
                    if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
                        cd ./nginx
						sed -e 's,{{namespace}},production,g;' nginx.yaml | kubectl apply -f -
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
                        cd ./nginx
						sed -e 's,{{namespace}},development,g;' nginx.yaml | kubectl apply -f -
						'''
					}
                }
            }
        }
        stage('Restart') {
            steps {
                sh '''
                cd ./kubernetes
                kubectl rollout restart deployment python-api-app
                kubectl rollout restart deployment nginx
                '''
            }
        }
    }
}
