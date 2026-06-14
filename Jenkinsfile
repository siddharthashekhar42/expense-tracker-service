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
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Tests') {
            steps {
                sh 'mvn test'
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
