pipeline {
    agent { label 'jenkins-slave' }
    environment {
        DOCKER_IMAGE = 'docker.io/ahmedmaher07/ivolve:v5'
        KUBE_NAMESPACE = 'prod'
        DEPLOYMENT_YAML = 'deployment.yaml'
    }
    stages {
        stage('Checkout') {
            steps {
                // Pull the code from the main branch of the repository
                git url: 'https://github.com/wolf8534/MultiBranch.git', branch: 'main'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Apply the deployment configuration to the Kubernetes cluster
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
                // Notify that the deployment was successful
                echo "Deployment to Kubernetes completed successfully!"
            }
        }
    }

    post {
        success {
            // Message displayed on successful pipeline execution
            echo "Pipeline executed successfully."
        }
        failure {
            // Message displayed on pipeline failure
            echo "Pipeline failed. Check the logs for errors."
        }
    }
}
