pipeline {
    agent any

    environment {
         MAVEN_HOME = '/opt/homebrew/Cellar/maven/3.9.9/libexec'
         PATH = "/opt/homebrew/bin:${MAVEN_HOME}/bin:${env.PATH}"
        DOCKERHUB_CREDENTIALS_ID = 'Docker_hub'
        DOCKERHUB_REPO = 'mahnoor95/sonar-scanner'
        DOCKER_IMAGE_TAG = 'latest_v1'
        DOCKERHUB_USER = 'mahnoor95'
        SONAR_TOKEN = credentials('sonar-scanner')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:MahnoorFatima02/SonarScanner.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
//                     docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}")
                sh """
                           docker buildx build \
                           --platform linux/amd64,linux/arm64 \
                           -t ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG} \
                           -- .
                       """
                }
            }
        }

                stage('Push Docker Image to Docker Hub') {
                    steps {
                        script {
                            withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS_ID, usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                                // Log in to Docker Hub
                                sh "echo ${DOCKERHUB_PASSWORD} | docker login -u ${DOCKERHUB_USER} --password-stdin"

                                // Push Docker images to Docker Hub
                                sh "docker push ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}"
                            }
                        }
                    }
                }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                        /opt/homebrew/bin/sonar-scanner \
                        -Dsonar.projectKey=devops-demo \
                        -Dsonar.sources=src \
                        -Dsonar.projectName=DevOps-Demo \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=${env.SONAR_TOKEN} \
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

    }
}
