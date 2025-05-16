pipeline {
    agent any

    environment {
        SONAR_PROJECT_KEY = "MERN-App"
        SONARQUBE_ENV = "SonarQubeServer"
        COMPOSE_FILE = 'compose.yml'
    }

    stages {
        stage('Code Analysis') {
            steps {
                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    withSonarQubeEnv("${SONARQUBE_ENV}") {
                        sh '''
                            sonar-scanner \
                              -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                              -Dsonar.sources=backend \
                              -Dsonar.branch.name=${BRANCH_NAME}
                        '''
                    }
                }
            }
        }

        stage('Build & Deploy with Docker Compose') {
            steps {
                script {
                    def tag = BRANCH_NAME.replaceAll('[^a-zA-Z0-9_.-]', '-').toLowerCase()

                    sh """
                        docker compose down || true
                        docker compose build --build-arg BRANCH_NAME=${tag}
                        docker compose up -d
                    """
                }
            }
        }
    }

    post {
        success {
            echo "MERN stack app deployed for branch ${env.BRANCH_NAME}!"
        }

        failure {
            echo "Deployment failed for branch ${env.BRANCH_NAME}!"
        }
    }
}
