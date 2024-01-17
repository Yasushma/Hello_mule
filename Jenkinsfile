pipeline {
    agent any
    tools {
        maven 'maven' // Adjust the version as needed
    }
   
    environment {
        MAVEN_HOME = tool 'maven'
        ARTIFACT_ACCESS_TOKEN = credentials('artifact_acesstoken')
        JFROG_CREDENTIALS = credentials('jfrog')
        JFROG_URL = 'https://hellomule21287.jfrog.io/artifactory/'
        //JFROG_REPO_MAVEN = 'hellomule-libs-release-local'
        JFROG_REPO_DOCKER = 'docker-trial'
        DOCKER_IMAGE_NAME = 'hello-mule'
        //DOCKER_IMAGE_TAG = '1.0.0'
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
                    sh "docker build -f Dockerfile -t ${DOCKER_IMAGE_NAME} ."
                    //docker.build("$DOCKER_IMAGE_NAME", '-f Dockerfile .')
                }
            }
        }
        

        stage('Deploy to Artifactory') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'ARTIFACT_ACCESS_TOKEN', variable: 'ARTIFACT_ACCESS_TOKEN')]) {
    sh "jfrog rt docker-push hello-mule docker-trial --build-name=Simple-Java-App --build-number=1 --url=https://hellomule21287.jfrog.io/artifactory/ --access-token=\$ARTIFACT_ACCESS_TOKEN"
}

            
                    // sh "jfrog rt upload --url ${JFROG_URL} --access-token ${ARTIFACT_ACCESS_TOKEN} target/*.jar ${JFROG_REPO_MAVEN}/"
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
