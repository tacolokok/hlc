pipeline {
    agent any

    environment {
        IMAGE_NAME = "txus27/hlc-app"
        KUBE_CONFIG = "/var/jenkins_home/.kube/config"
    }

    stages {
        stage('Clonar repo') {
            steps {
                git branch: 'main', url: 'https://github.com/tacolokok/hlc.git'
            }
        }

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
                        docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:latest
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
