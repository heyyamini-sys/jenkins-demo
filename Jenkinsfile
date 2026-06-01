    agent any
    environment {
        APP_NAME = "my-flask-app"
        PORT = "5000"
    }
    stages {
        stage("Checkout") {
            steps {
                git branch: "main", url: "https://github.com/heyyamini-sys/jenkins-demo.git"
            }
        }
        stage("Build Docker Image") {
            steps {
                script {
                    sh "docker build -t ${APP_NAME}:latest ."
                }
            }
        }
        stage("Test") {
            steps {
                script {
                    sh """
                        docker run -d --name ${APP_NAME}-test -p 5001:5000 ${APP_NAME}:latest
                        sleep 3
                        curl -f http://localhost:5001/ || exit 1
                        docker stop ${APP_NAME}-test
                        docker rm ${APP_NAME}-test
                    """
                }
            }
        }
        stage("Deploy") {
            steps {
                script {
                    sh """
                        docker stop ${APP_NAME} || true
                        docker rm ${APP_NAME} || true
                        docker run -d --name ${APP_NAME} -p ${PORT}:5000 --restart unless-stopped ${APP_NAME}:latest
                    """
                }
            }
        }
    }
    post {
        failure { echo "Pipeline failed! Check logs." }
        success { echo "Pipeline succeeded! App deployed on port 5000." }
    }
