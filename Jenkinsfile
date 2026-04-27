pipeline {
    agent any

    environment {
        NETWORK = 'giniminds-bridge'
        PROJECT = 'cp-8'
    }

    stages {

        stage('Debug Workspace') {
            steps {
                sh '''
                echo "===== USER ====="
                whoami

                echo "===== WORKING DIR ====="
                pwd

                echo "===== FILES IN WORKSPACE ====="
                ls -la
                '''
            }
        }

        // ❗ Remove if using "Pipeline script from SCM"
        stage('Clone (SCM)') {
            steps {
                checkout scm
            }
        }

        stage('Validate YAML Files') {
            steps {
                sh '''
                echo "===== CHECK YAML FILES ====="
                ls -l cp.yml || echo "❌ cp.yml NOT FOUND"
                ls -l c3.yml || echo "❌ c3.yml NOT FOUND"
                '''
            }
        }

        stage('Check Docker Access') {
            steps {
                sh '''
                echo "===== DOCKER CHECK ====="
                docker --version || echo "❌ Docker not installed"
                docker compose version || echo "❌ Docker Compose missing"
                docker ps || echo "❌ Docker permission issue"
                '''
            }
        }

        stage('Ensure Docker Network') {
            steps {
                sh '''
                echo "===== NETWORK CHECK ====="
                docker network inspect ${NETWORK} >/dev/null 2>&1 || \
                docker network create ${NETWORK}

                docker network ls | grep ${NETWORK}
                '''
            }
        }

        stage('Clean Previous Run') {
            steps {
                sh '''
                echo "===== CLEANUP (SAFE) ====="
                docker compose -p ${PROJECT} -f cp.yml down || true
                docker compose -p ${PROJECT} -f c3.yml down || true
                '''
            }
        }

        stage('Start Core Stack (cp.yml)') {
            steps {
                sh '''
                echo "===== STARTING CP STACK ====="
                docker compose -p ${PROJECT} -f cp.yml up -d
                '''
            }
        }

        stage('Start Control Center (c3.yml)') {
            steps {
                sh '''
                echo "===== STARTING C3 STACK ====="
                docker compose -p ${PROJECT} -f c3.yml up -d
                '''
            }
        }

        stage('Stabilization Wait') {
            steps {
                sh 'sleep 30'
            }
        }

        stage('Verification') {
            steps {
                sh '''
                echo "===== RUNNING CONTAINERS ====="
                docker ps

                echo "===== COMPOSE PROJECTS ====="
                docker compose ls

                echo "===== NETWORK DETAILS ====="
                docker network inspect ${NETWORK} || true
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Stack deployed successfully'
        }
        failure {
            echo '❌ Deployment failed — check logs above (look for first error)'
        }
    }
}
