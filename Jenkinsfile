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
                script {
                    git url: 'https://github.com/wolf8534/MultiBranch.git', branch: 'third'
                }
            }
        }

       
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'k8s_token', variable: 'K8S_TOKEN')]) {
                        sh """
                        kubectl --kubeconfig=/home/ubuntu/jenkins/.kube/config apply -f $DEPLOYMENT_YAML -n $KUBE_NAMESPACE
                        """
                    }
                }
            }
        }

        stage('Post Action') {
            steps {
                script {
                    echo "Deployment to Kubernetes completed successfully!"
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
