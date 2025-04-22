pipeline {
    agent any

    environment {
        IMAGE_NAME = "tacolokok/hlc"
        KUBE_CONFIG = "/var/jenkins_home/.kube/config"
    }

    stages {
        stage('Build imagen Docker') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Push a DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy en Kubernetes') {
            steps {
                sh 'kubectl --kubeconfig=$KUBE_CONFIG apply -f k8s/'
            }
        }
    }
}
