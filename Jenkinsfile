@Library('my-jenkins-shared-lib@main') _

pipeline {
    agent {
        label 'aws-ec2-agent'
    }
    
    environment {
        DOCKER_IMAGE_NAME = "mujimmy/openjdk" 
        DOCKER_IMAGE_VERSION = "${BUILD_NUMBER}"
        SONAR_PROJECT_KEY = "my-gradle-project-key"
        SONAR_PROJECT_NAME = "My Gradle Project"
        SONAR_HOST_URL = "http://54.221.92.185:9000"
        DEPLOYMENT_NAME= "my-deployment"
        CONTAINER_NAME="my-container"
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }
         stage('Set Permissions') {
            steps {
              sh 'chmod +x gradlew'
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
               deployToKindStage()
             }
           }
        }
     }
}
