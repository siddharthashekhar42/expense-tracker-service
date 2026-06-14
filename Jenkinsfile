pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
    }

    environment {
        APP_NAME = "expense-tracker-service"
        BUILD_VERSION = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
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
                // sh 'mvn verify'
            }
        }

        stage('Package Artifact') {
            steps {
                echo "Packaging artifact for Spinnaker"
                sh "cp target/${APP_NAME}-0.0.1-SNAPSHOT.jar target/${APP_NAME}-${BUILD_VERSION}.jar"
            }
        }

        stage('Publish Artifact Metadata') {
            steps {
                echo "Publishing artifact metadata for Spinnaker trigger"
                writeFile file: "artifact.json", text: """
                {
                  "app": "${APP_NAME}",
                  "version": "${BUILD_VERSION}",
                  "branch": "${env.BRANCH_NAME}",
                  "timestamp": "${new Date().getTime()}"
                }
                """
                archiveArtifacts artifacts: 'artifact.json', fingerprint: true
            }
        }

        stage('Notify Spinnaker') {
            when {
                expression {
                    env.BRANCH_NAME == "develop" ||
                    env.BRANCH_NAME.startsWith("release/") ||
                    env.BRANCH_NAME == "master"
                }
            }
            steps {
                echo "Triggering Spinnaker pipeline for ${env.BRANCH_NAME}"
                // curl -X POST http://spinnaker/api/v1/pipelines/trigger ...
            }
        }
    }

    post {
        success {
            echo "CI SUCCESS for ${env.BRANCH_NAME}"
        }
        failure {
            echo "CI FAILED for ${env.BRANCH_NAME}"
        }
    }
}
