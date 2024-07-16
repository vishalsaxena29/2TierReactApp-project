pipeline {
    agent any
    environment {
        DOCKER_HUB_TOKEN = credentials('docker-access-token')
        DOCKER_HUB_USERNAME = credentials('docker-hub-username')
        GIT_REPO_URL = 'https://github.com/vishalsaxena29/two-tier-app2.git'
        GIT_USER_EMAIL = 'saxena.vishal204@gmail.com' 
        GIT_USER_NAME = 'vishalsaxena29' 
        GITHUB_TOKEN = credentials('GITHUB') 
    }
    stages {
        stage('Set Image Names') {
            steps {
                script {
                    env.IMAGE_NAME_FRONTEND = "${DOCKER_HUB_USERNAME}/frontend-app"
                    env.IMAGE_NAME_BACKEND = "${DOCKER_HUB_USERNAME}/backend-app"
                }
            }
        }
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${GIT_REPO_URL}"
            }
        }
        stage('Build Frontend Docker Image') {
            steps {
                script {
                    dir('frontend') {
                        sh 'docker build -t ${IMAGE_NAME_FRONTEND}:${BUILD_NUMBER} .'
                    }
                }
            }
        }
        stage('Build Backend Docker Image') {
            steps {
                script {
                    dir('backend') {
                        sh 'docker build -t ${IMAGE_NAME_BACKEND}:${BUILD_NUMBER} .'
                    }
                }
            }
        }
        stage('Push Frontend Docker Image to Docker Hub') {
            steps {
                script {
                    sh 'echo ${DOCKER_HUB_TOKEN} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin'
                    sh 'docker push ${IMAGE_NAME_FRONTEND}:${BUILD_NUMBER}'
                }
            }
        }
        stage('Push Backend Docker Image to Docker Hub') {
            steps {
                script {
                    sh 'echo ${DOCKER_HUB_TOKEN} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin'
                    sh 'docker push ${IMAGE_NAME_BACKEND}:${BUILD_NUMBER}'
                }
            }
        }
        stage('Update Deployment Files') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'GITHUB', variable: 'GITHUB_TOKEN')]) {
                        sh '''
                            git config user.email "${GIT_USER_EMAIL}"
                            git config user.name "${GIT_USER_NAME}"
                            BUILD_NUMBER=${BUILD_NUMBER}
                            sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" Kubernetes-Manifest-files/Backend/deployment.yaml
                            sed -i "s|${DOCKER_HUB_USERNAME}/frontend:replaceImageTag|${IMAGE_NAME_FRONTEND}:${BUILD_NUMBER}|g" Kubernetes-Manifest-files/Frontend/deployment.yaml
                            git add Kubernetes-Manifest-files/Backend/deployment.yaml
                            git add Kubernetes-Manifest-files/Frontend/deployment.yaml
                            git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                            git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/two-tier-app2 HEAD:main
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'docker logout'
            }
        }
        success {
            echo 'Build and push succeeded!'
        }
        failure {
            echo 'Build or push failed.'
        }
    }
}
