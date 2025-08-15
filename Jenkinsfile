pipeline{
    agent any

    tools {
        maven 'maven s/w'
    }

    environment {
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
        DOCKER_IMAGE = "ashwiniboddu/banking:latest"
        DOCKER_CREDENTIALS = 'dockerhub-cred'
    }

    stages {
        stage ('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ashwiniboddu/Banking.git'
            }
        }
        stage ('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage ('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        stage ('Push Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('',DOCKER_CREDENTIALS) {
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        stage ('Deploy it in Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                kubectl apply -f pv.yaml
                kubectl apply -f pvc.yaml
                '''
            }
        }
    }
    post {
        success {
            echo "Deployment successfully completed"
        }
        failure {
            echo "Deployment failed"
        }
    }
}
