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
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                if [ "$BACKEND_COUNT" = "1" ]; then
                    docker run -d --name backend1 backend-app
                else
                    docker run -d --name backend1 backend-app
                    docker run -d --name backend2 backend-app
                fi
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker run -d --name nginx -p 8081:80 nginx
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

