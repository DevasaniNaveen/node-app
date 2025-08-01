pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                sh 'echo passed'
                //git url: 'https://github.com/DevasaniNaveen/node-app.git', branch: 'main'
            }
        }

        stage('Static Code Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=node-app \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=http://3.83.11.39:9000 \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t node-app .'
            }
        }

       stage('Docker Cleanup') {
            steps {
                sh '''
                    # Get container using port 8082 and stop it
                    CONTAINER_ID=$(docker ps -q --filter "publish=8082")
                    if [ ! -z "$CONTAINER_ID" ]; then
                      echo "Stopping container using port 8082: $CONTAINER_ID"
                      docker stop $CONTAINER_ID
                      docker rm $CONTAINER_ID
                    fi
                '''
            }
        }

        stage('Docker Run') {
            steps {
                sh 'docker run -d -p 8082:3000 node-app'
            }
        }
    }
}
