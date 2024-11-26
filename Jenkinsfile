pipeline {
    agent any
    tools {
        jdk 'jdk'  
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/ezimzi/maven-java-sample-app.git'
            }
        }
        stage('compile') {
            steps {
                bat 'maven spring-javaformat:apply'
                bat 'maven clean compile'
            }
        }
        stage('built') {
            steps {
                bat 'maven package'
            }
        }
        stage('push docker image') {
            steps {
               script {
                    // Docker image details
                    def dockerImage = 'imtiyaz18/maven-java-sample-app'
                    def dockerTag = 'latest'

                    // Docker login
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    }

                    // Build the Docker image
                    bat "docker build -t ${dockerImage}:${dockerTag} ."

                    // Push the Docker image to Docker Hub
                    bat "docker push ${dockerImage}:${dockerTag}"
                } 
            }
        }
        stage('deploy application') {
            steps {
                script {
                    // Pull and run the Docker container
                    def dockerImage = 'imtiyaz18/maven-java-sample-app'
                    def dockerTag = 'latest'
                    bat "docker pull ${dockerImage}:${dockerTag}"
                    bat "docker run -d -p 8001:8001 --name maven-app ${dockerImage}:${dockerTag}"
                }
            }
        }
    }
}
