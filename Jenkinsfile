pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'rishijain20'
        DOCKER_IMAGE_WEB = 'web-service'
        DOCKER_IMAGE_WORKER = 'worker-service'
        
        SONARQUBE_URL = 'SonarQube'
        NODE_VERSION = 'NodeJS-20'
    }

    triggers {
        githubPush()  // Auto-build on Git push
    }

    stages {
        stage('Repo cloning') {
            steps {
                git branch: 'main', url: 'https://github.com/akhilesh-1306/DevOps-Assignment-2'
            }
        }

        stage('Building services') {
            steps {
                script {
                    def nodejs = tool name: "${NODE_VERSION}", type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodejs}/bin;${env.PATH}"
                }
                bat '''
                cd web-service && npm install
                cd ../worker-service && npm install
                '''
            }
        }

        stage('SonarQube analysis') {
            steps {
                script {
                    withSonarQubeEnv("${SONARQUBE_URL}") {
                        bat '''
                        cd web-service && npm run sonar  
                        cd ../worker-service && npm run sonar  
                        '''
                    }
                }
            }
        }

        stage('Testing services') {
            steps {
                bat '''
                cd web-service && npm test  
                cd ../worker-service && npm test  
                '''
            }
        }

        stage('Building images') {
            steps {
                bat '''
                docker build -t %DOCKER_IMAGE_WEB% ./web-service  
                docker build -t %DOCKER_IMAGE_WORKER% ./worker-service  
                '''
            }
        }

        stage('Pushing images to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-bcd7', url: 'https://index.docker.io/v1/']) {
                    bat '''
                    docker tag %DOCKER_IMAGE_WEB% %DOCKER_HUB_USER%/%DOCKER_IMAGE_WEB%
                    docker tag %DOCKER_IMAGE_WORKER% %DOCKER_HUB_USER%/%DOCKER_IMAGE_WORKER%

                    docker push %DOCKER_HUB_USER%/%DOCKER_IMAGE_WEB%  
                    docker push %DOCKER_HUB_USER%/%DOCKER_IMAGE_WORKER%  
                    '''
                }
            }
        }

        stage('Running containers') {
            steps {
                bat 'docker-compose up -d'
            }
        }

        stage('Cleanup') {
            steps {
                bat 'docker system prune -f'  // Cleans up unused Docker data
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
