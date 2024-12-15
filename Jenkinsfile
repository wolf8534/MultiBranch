pipeline {
    agent { label 'jenkins-slave' }
    environment {
        DOCKER_IMAGE = 'docker.io/ahmedmaher07/ivolve:v3'
        KUBE_NAMESPACE = 'dev'
        DEPLOYMENT_YAML = 'deployment.yaml'
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git url: 'https://github.com/wolf8534/MultiBranch.git', branch: 'second'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Docker_hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Edit Deployment.yaml') {
            steps {
                script {
                    sh "sed -i 's|image:.*|image: $DOCKER_IMAGE|g' $DEPLOYMENT_YAML"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'k8s_token', variable: 'K8S_TOKEN')]) {
                        sh """
                        kubectl --kubeconfig=/home/ubuntu/jenkins/.kube/config  apply -f $DEPLOYMENT_YAML -n $KUBE_NAMESPACE
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
