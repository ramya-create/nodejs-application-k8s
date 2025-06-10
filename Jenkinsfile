pipeline {
    agent any

    tools {
        nodejs 'Node18'
    }

    environment {
        DOCKER_IMAGE = 'my-node-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/ramya-create/nodeJs-application.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t my-node-app .'
            }
        }
    //    For run the docker
    //     stage('Run Docker Container') {
    //         steps {
    //             bat 'docker rm -f node-app || true'
    //             bat 'docker run -d --name node-app -p 3000:3000 my-node-app'
    //         }
    //     }
    // }

        // For k8s integratiom
        stage('Check Kubernetes Cluster') {
          steps {
            bat 'kubectl config current-context'
            bat 'kubectl cluster-info'
            bat 'kubectl get nodes'
          }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def deleteStatus = bat(script: 'kubectl delete -f deployment.yaml', returnStatus: true)
                    if (deleteStatus != 0) {
                        echo 'No existing deployment to delete or deletion failed, continuing...'
                    }
                }
                bat 'kubectl apply -f deployment.yaml'
            }
        }

        stage('Expose Service') {
            steps {
                bat 'kubectl apply -f service.yaml'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'kubectl get pods'
                bat 'kubectl get svc'
            }
        }
        stage('Access App') {
            steps {
                bat 'minikube service my-node-service'
            }
        }
    }
    post {
        always {
            echo 'Cleaning workspace and releasing agent...'
            cleanWs()
        }
        success {
            echo '✅ Build and deployment successful!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
