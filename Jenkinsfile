pipeline {
    agent any

    stages {

        stage('Create Docker Network') {
            steps {
                sh '''
                docker network create lab6-network || true
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 --network lab6-network backend-app
                docker run -d --name backend2 --network lab6-network backend-app

                sleep 5
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d --name nginx-lb \
                --network lab6-network \
                -p 80:80 nginx

                sleep 3

                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf

                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}