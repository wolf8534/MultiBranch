pipeline {
    agent { label 'jenkins-slave' }
    environment {
        DOCKER_IMAGE = 'docker.io/ahmedmaher07/ivolve:v9'
        KUBE_NAMESPACE = 'dev'
        DEPLOYMENT_YAML = 'deployment.yaml'
    }
    stages {
        stage('Checkout') {
            steps {
                // Pull the code from the specified branch in the repository
                git url: 'https://github.com/wolf8534/MultiBranch.git', branch: 'second'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build the Docker image using the specified tag
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Push Docker Image') {
            steps {
                // Log in to Docker Hub and push the image
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
                // Update the image tag in the Kubernetes deployment YAML
                sh "sed -i 's|image:.*|image: $DOCKER_IMAGE|g' $DEPLOYMENT_YAML"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // Apply the deployment to the Kubernetes cluster
                withCredentials([string(credentialsId: 'k8s_token', variable: 'K8S_TOKEN')]) {
                    sh """
                    kubectl create namespace $KUBE_NAMESPACE || true
                    kubectl --kubeconfig=/home/ubuntu/jenkins/.kube/config apply -f $DEPLOYMENT_YAML -n $KUBE_NAMESPACE
                    """
                }
            }
        }
        stage('Post Action') {
            steps {
                // Notify of successful deployment
                echo "Deployment to Kubernetes completed successfully!"
            }
        }
    }
    post {
        success {
            // Success message in case of successful pipeline execution
            echo "Pipeline executed successfully."
        }
        failure {
            // Failure message in case of pipeline failure
            echo "Pipeline failed. Check the build logs for details."
        }
    }
}
