pipeline {
    agent any

    environment {
        IMAGE_NAME = "amdp-registry.skala-ai.com/skala26a-ai2/sk032-devops-1day-app"
        IMAGE_TAG = "1.0.${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = "harbor-registry-creds"
    }

    stages {
        stage('Build Code') {
            steps {
                sh 'python3 --version'
                sh 'docker --version'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Push To Harbor') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${REGISTRY_CREDENTIALS}",
                    usernameVariable: 'HARBOR_USER',
                    passwordVariable: 'HARBOR_PASSWORD'
                )]) {
                    sh 'echo "${HARBOR_PASSWORD}" | docker login amdp-registry.skala-ai.com -u "${HARBOR_USER}" --password-stdin'
                    sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
                    sh 'docker logout amdp-registry.skala-ai.com'
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed: ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
