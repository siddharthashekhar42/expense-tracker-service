pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo "Branch detected: ${env.BRANCH_NAME}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Unit Tests') {
            when {
                expression {
                    env.BRANCH_NAME.startsWith("feature/") ||
                    env.BRANCH_NAME == "develop" ||
                    env.BRANCH_NAME.startsWith("release/")
                }
            }
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Tests') {
            when {
                expression { env.BRANCH_NAME == "develop" }
            }
            steps {
                echo "Running integration tests for develop branch"
                // sh 'mvn verify'  // enable when ready
            }
        }

        stage('Prepare Release Artifact') {
            when {
                expression { env.BRANCH_NAME.startsWith("release/") }
            }
            steps {
                echo "Preparing release candidate artifact"
                // sh 'mvn clean package'
            }
        }

        stage('Production Build') {
            when {
                expression { env.BRANCH_NAME == "master" }
            }
            steps {
                echo "Building production artifact"
                sh 'mvn clean package'
            }
        }
    }

    post {
        success {
            echo "Build SUCCESS on branch: ${env.BRANCH_NAME}"
        }
        failure {
            echo "Build FAILED on branch: ${env.BRANCH_NAME}"
        }
    }
}
