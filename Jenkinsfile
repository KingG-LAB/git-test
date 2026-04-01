pipeline {
    agent any

    environment {
        IMAGE_NAME = "amdp-registry.skala-ai.com/skala26a-ai2/sk032-backend"
        IMAGE_TAG = "1.0.${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = "harbor-registry-creds"
        K8S_NAMESPACE = "class-2"
        DEPLOYMENT_NAME = "sk032-backend"
        CONTAINER_NAME = "app"
        IMAGE_PLATFORM = "linux/amd64"
    }

    stages {
        stage('Build Code') {
            steps {
                sh 'python3 --version'
                sh 'docker --version'
                sh 'docker buildx version'
                sh 'kubectl version --client=true'
            }
        }

        stage('Build And Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${REGISTRY_CREDENTIALS}",
                    usernameVariable: 'HARBOR_USER',
                    passwordVariable: 'HARBOR_PASSWORD'
                )]) {
                    sh 'echo "${HARBOR_PASSWORD}" | docker login amdp-registry.skala-ai.com -u "${HARBOR_USER}" --password-stdin'
                    sh 'docker buildx create --name jenkins-builder --use >/dev/null 2>&1 || docker buildx use jenkins-builder'
                    sh 'docker buildx build --platform ${IMAGE_PLATFORM} -t ${IMAGE_NAME}:${IMAGE_TAG} --push .'
                    sh 'docker logout amdp-registry.skala-ai.com'
                }
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
                sh 'kubectl set image deployment/${DEPLOYMENT_NAME} ${CONTAINER_NAME}=${IMAGE_NAME}:${IMAGE_TAG} -n ${K8S_NAMESPACE}'
                sh 'kubectl rollout status deployment/${DEPLOYMENT_NAME} -n ${K8S_NAMESPACE} --timeout=180s'
                sh 'kubectl get pods -n ${K8S_NAMESPACE}'
            }
        }
    }

    post {
        success {
            echo "Pipeline completed and deployed: ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
