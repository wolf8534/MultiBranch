pipeline {
    agent { label 'jenkins-slave' }
    environment {
        DOCKER_IMAGE = 'docker.io/ahmedmaher07/ivolve:v6'
        KUBE_NAMESPACE = 'test'
        DEPLOYMENT_YAML = 'deployment.yaml'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/wolf8534/MultiBranch.git', branch: 'third'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Docker_hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh """
                    docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                    docker push $DOCKER_IMAGE
                    """
                }
            }
        }
        stage('Edit Deployment.yaml') {
            steps {
                sh "sed -i 's|image:.*|image: $DOCKER_IMAGE|g' $DEPLOYMENT_YAML"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([string(credentialsId: 'k8s_token', variable: 'K8S_TOKEN')]) {
                    sh """
                    kubectl --kubeconfig=/home/ubuntu/jenkins/.kube/config apply -f $DEPLOYMENT_YAML -n $KUBE_NAMESPACE
                    """
                }
            }
        }
        stage('Post Action') {
            steps {
                echo "Deployment to Kubernetes completed successfully!"
            }
        }
    }
    post {
        success {
            echo "Pipeline executed successfully."
        }
        failure {
            echo "Pipeline failed. Check logs for more details."
        }
    }
}
