pipeline {
    agent any
    tools {
        maven 'maven' // Adjust the version as needed
    }
   
    environment {
        MAVEN_HOME = tool 'maven'
        ARTIFACT_ACCESS_TOKEN = credentials('artifact_acesstoken')
        JFROG_CREDENTIALS = credentials('jfrog')
        JFROG_URL = 'https://joslin2024.jfrog.io/artifactory'
        JFROG_REPO_DOCKER = 'muledocker-docker'
        DOCKER_IMAGE_NAME = 'simple_mule'
		DOCKER_ACCESS_TOKEN = credentials('docker_jfrog_token')
        }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/Yasushma/Hello_mule.git']]])
            }
        }

        stage('Build') {
            steps {
                script {
                    sh "${MAVEN_HOME}/bin/mvn clean package"
                     sh 'mv target/*.jar target/newapp.jar'
                }
            }
        }
        stage('Build Docker Image') {    
            steps {
                script {
                    withCredentials([string(credentialsId: 'artifact_acesstoken', variable: 'ARTIFACT_ACCESS_TOKEN')]) {
                        // Build the Docker image
                        sh "docker build -f Dockerfile -t ${DOCKER_IMAGE_NAME} ."
                        
						sh "docker login -u sushma -p ${DOCKER_ACCESS_TOKEN} joslin2024.jfrog.io"
                        // Tag the Docker image
                        sh "docker tag ${DOCKER_IMAGE_NAME}:latest ${JFROG_URL}/${JFROG_REPO_DOCKER}/${DOCKER_IMAGE_NAME}:latest"
                        
                        // Push the Docker image to Artifactory
                        
                        
                        sh "docker push ${JFROG_URL}/${JFROG_REPO_DOCKER}/${DOCKER_IMAGE_NAME}:latest"
                          sh " docker images "
                    }
                    //sh "docker build -f Dockerfile -t ${DOCKER_IMAGE_NAME} ."
                    //docker.build("$DOCKER_IMAGE_NAME", '-f Dockerfile .')
                   // sh 'docker login -usushma joslin2024.jfrog.io'
                   //sh "docker build -t joslin2024.jfrog.io/artifactory/muledocker-docker-local/hello-mule:${BUILD_NUMBER} --pull ."

                    
           
                }
            }
        }
        

        
        /*stage('Build Docker Image') {
            steps {
                script {
                    // Promote the build to trigger Docker image build
                    sh "jfrog rt build-promote hello-mule 1.0.0 --status=Staged"
                }
            }
        }*/
    }
}
