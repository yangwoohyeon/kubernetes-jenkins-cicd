pipeline {
    agent any

    environment {
        GIT_URL = "https://github.com/yangwoohyeon/kubernetes-jenkins-cicd.git"
        DOCKER_IMAGE = "yangwoohyeon/jenkins-pipeline:latest"
    }

    stages {
        stage('Clone') {
            steps {
                git url: "${GIT_URL}", branch: 'main'
            }
        }

        stage('Setup') {
            steps {
                sh 'chmod +x gradlew || true'
            }
        }

        stage('Build') {
            steps {
                sh './gradlew build'
                sh 'ls build/libs'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build --no-cache -t ${DOCKER_IMAGE} ."
            }
        }

        stage('DockerHub Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }

        stage('CleanUp') {
            steps {
                sh "docker images -qf dangling=true | xargs -r docker rmi || true"
            }
        }
    }
}
