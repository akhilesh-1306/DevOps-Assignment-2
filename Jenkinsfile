pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'akhileshnekar'
        DOCKER_IMAGE_WEB = 'web-service'
        DOCKER_IMAGE_WORKER = 'worker-service'
        
        SONARQUBE_URL = 'SonarQube'
        NODE_VERSION = 'NodeJS-20'
    }

    triggers {
        githubPush()  // Auto-build on Git push
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/akhilesh-1306/DevOps-Assignment-2'
            }
        }

        stage('Build Web and Worker Services') {
            steps {
                script {
                    def nodejs = tool name: "${NODE_VERSION}", type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodejs}/bin:${env.PATH}"
                }
                sh '''
                cd web-service && npm install
                cd ../worker-service && npm install
                '''
            }
        }

        stage('Code Analysis with SonarQube') {
            steps {
                script {
                    withSonarQubeEnv("${SONARQUBE_URL}") {
                        sh '''
                        # Ensure 'npm run sonar' is correctly configured in package.json for both services
                        cd web-service && npm run sonar  
                        cd ../worker-service && npm run sonar  
                        '''
                    }
                }
            }
        }

        stage('Test Web and Worker Services') {
            steps {
                sh '''
                # Ensure your test scripts are properly set in package.json
                cd web-service && npm test  
                cd ../worker-service && npm test  
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                # Build Docker images
                docker build -t ${DOCKER_IMAGE_WEB} ./web-service  
                docker build -t ${DOCKER_IMAGE_WORKER} ./worker-service  
                '''
            }
        }

        stage('Tag and Push Docker Images to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/']) {
                    sh '''
                    # Tag images with Docker Hub username
                    docker tag ${DOCKER_IMAGE_WEB} ${DOCKER_HUB_USER}/${DOCKER_IMAGE_WEB}
                    docker tag ${DOCKER_IMAGE_WORKER} ${DOCKER_HUB_USER}/${DOCKER_IMAGE_WORKER}

                    # Push images to Docker Hub
                    docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE_WEB}  
                    docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE_WORKER}  
                    '''
                }
            }
        }

        stage('Run Docker Containers') {
            steps {
                sh 'docker-compose up -d'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker system prune -f'  // Cleans up unused Docker data
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
