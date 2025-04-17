pipeline {
    agent any

    environment {
         MAVEN_HOME = '/opt/homebrew/Cellar/maven/3.9.9/libexec'
         PATH = "/opt/homebrew/bin:${MAVEN_HOME}/bin:${env.PATH}"
        DOCKERHUB_CREDENTIALS_ID = 'Docker_hub'
        DOCKERHUB_REPO = 'mahnoor95/shoppingcart'
        DOCKER_IMAGE_TAG = 'latest_v2'
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
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    bat """
                        sonar-scanner ^
                        -Dsonar.projectKey=devops-demo ^
                        -Dsonar.sources=src ^
                        -Dsonar.projectName=DevOps-Demo ^
                        -Dsonar.host.url=http://localhost:9000 ^
                        -Dsonar.login=${env.SONAR_TOKEN} ^
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

    }
}
