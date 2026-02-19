pipeline {
    agent any

    parameters {
        choice(name: 'BACKEND_COUNT', choices: ['1', '2'], description: 'Number of backend containers')
    }

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rm -f backend1 backend2 nginx || true
                docker network rm mynetwork || true
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker network create mynetwork

                if [ "$BACKEND_COUNT" = "1" ]; then
                    docker run -d --name backend1 --network mynetwork backend-app
                else
                    docker run -d --name backend1 --network mynetwork backend-app
                    docker run -d --name backend2 --network mynetwork backend-app
                fi
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker run -d --name nginx \
                --network mynetwork \
                -p 8081:80 \
                -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                nginx
                '''
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check console logs.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
    }
}
