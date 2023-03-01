pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Hello World from Build stage'
                script {
                    docker.build("jenkins-maven-docker")
                }
            }
        }
        stage('Push') {
            steps {
                echo 'Hello World from Build stage'
                sh '''
                    aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 863404411791.dkr.ecr.us-west-2.amazonaws.com
                    docker tag jenkins-maven-docker 863404411791.dkr.ecr.us-west-2.amazonaws.com/jenkins-maven-docker:v${BUILD_NUMBER}
                    docker push 863404411791.dkr.ecr.us-west-2.amazonaws.com/jenkins-maven-docker:v${BUILD_NUMBER}
                '''
            }
        }
        stage('Deploy') {
            input {
                message "Are you sure you wanna deploy????"
            }
            steps {
                echo 'Hello World from Deploy stage'
                sh '''
                    aws eks update-kubeconfig --name feb-tf-eks --region us-west-2
                    rm -rf deployments
                    mkdir deployments
                    sed "s/<TAG>/${BUILD_NUMBER}/g" templates/deployment.yaml > deployments/backend.yaml
                    kubectl apply -R -f deployments/
                '''
            }
        }
    }
}
