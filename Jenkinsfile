pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat './mvnw clean package -DskipTests'
            }
        }

        stage('Tests') {
            steps {
                bat './mvnw test'
            }
        }
    }

    post {
        success {
            echo "Build OK on branch: ${env.BRANCH_NAME}"
        }
        failure {
            echo "Build FAILED on branch: ${env.BRANCH_NAME}"
        }
    }
}
