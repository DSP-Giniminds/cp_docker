pipeline {
    agent any

    environment {
        NETWORK = 'giniminds-bridge'
    }

    stages {

        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Ensure Docker Network') {
            steps {
                sh """
                docker network inspect ${NETWORK} >/dev/null 2>&1 || \
                docker network create ${NETWORK}
                """
            }
        }

        stage('Clean Previous Run (Safe Reset)') {
            steps {
                sh """
                docker compose -f cp.yml down -v || true
                docker compose -f c3.yml down -v || true
                """
            }
        }

        stage('Start Core Stack (cp.yml)') {
            steps {
                sh "docker compose -f cp.yml up -d"
            }
        }

        stage('Start Control Center (c3.yml)') {
            steps {
                sh "docker compose -f c3.yml up -d"
            }
        }

        stage('Stabilization Wait') {
            steps {
                sh "sleep 30"
            }
        }

        stage('Verification') {
            steps {
                sh """
                docker ps
                docker network inspect ${NETWORK}
                """
            }
        }
    }

    post {
        success {
            echo 'Stack deployed successfully'
        }
        failure {
            echo 'Deployment failed — check logs'
        }
    }
}
