pipeline {
    environment {
    IMAGE_NAME = "ic-webapp-webapp"
		IMAGE_TAG = "latest"
    DOCKER_USER = "lyk1719"
		STAGING = "lyk1719-staging"
		PRODUCTION = "lyk1719-production"
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
    agent none
    stages {
        stage("Build image") {
            agent any
            steps {
                script {
                  //sh """docker-compose build -t ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."""
                  sh """docker-compose build"""
                }
            }
        }
        stage("Run the container based on the built image") {
            agent any
            steps {
                script {
                    sh """docker-compose up -d"""
                }
            }
        }
        stage("Test image") {
            agent any
            steps {
                script {
                    sh """
                        curl -I http://localhost:7080 | grep "200"
                    """
                }
            }
        }
        //stage("Clean contaier") {
        //    agent any
        //    steps {
        //        script {
        //            sh """
        //                docker stop ${IMAGE_NAME}
        //                docker rm ${IMAGE_NAME}
        //            """
        //        }
        //    }
        //}
        stage('Docker login') {
            agent any
            steps {
                sh """echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"""
            }
        }
        stage('Push') {
            agent any
            steps {
                script {
                    sh """docker push ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}"""
                }
            }
        }
        stage('Logout') {
            agent any
            steps {
                script {
                    sh """docker logout"""
                }
            }
        }
    }
    post {
        success {
            slackSend (color: '#00FF00', message: "Jules - SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        failure {
            slackSend (color: '#FF0000', message: "Jules - FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
    }
}
