@Library('my-jenkins-shared-lib@main') _ // Replace 'my-jenkins-shared-lib' with your repo name and main with branch

pipeline {
    agent {
        label 'jenkins-agent'   // Replace with your agent label
    }
    
    environment {
        DOCKER_IMAGE_NAME = "mujimmy/openjdk" // Replace
        DOCKER_IMAGE_VERSION = "${BUILD_NUMBER}"
        SONAR_PROJECT_KEY = "my-gradle-project-key"
        SONAR_PROJECT_NAME = "My Gradle Project"
        SONAR_HOST_URL = "http://localhost:9000" // Replace with your SonarQube URL
        DEPLOYMENT_NAME= "my-deployment"
        CONTAINER_NAME="my-container"
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Unit Test') {
            steps {
                sh './gradlew test'
            }
        }

        stage('Build JAR') {
          steps {
            sh './gradlew assemble'
          }
        }

       stage('SonarQube Test') {
        steps {
            script {
              sonarScan(
                 projectKey: "${SONAR_PROJECT_KEY}",
                 projectName: "${SONAR_PROJECT_NAME}",
                 sonarHostUrl: "${SONAR_HOST_URL}"
                )
            }
        }
      }

        stage('Build Image') {
            steps {
                script {
                  dockerBuild(
                      imageName: "${DOCKER_IMAGE_NAME}",
                      version: "${DOCKER_IMAGE_VERSION}"
                  )
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                script {
                    dockerPush(
                      imageName: "${DOCKER_IMAGE_NAME}",
                      version: "${DOCKER_IMAGE_VERSION}"
                  )
                }
            }
        }
      
        stage('Deploy to Kind') {
            steps {
               script {
                 deployToKind(
                    imageName: "${DOCKER_IMAGE_NAME}",
                    version: "${DOCKER_IMAGE_VERSION}",
                    deploymentName: "${DEPLOYMENT_NAME}",
                    containerName: "${CONTAINER_NAME}"
                 )
              }
            }
        }
    }
}
