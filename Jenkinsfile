pipeline {
    agent any
    tools {
        jdk 'jdk'       // Reference your JDK configuration
        maven 'maven'   // Reference the Maven configuration name
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/ezimzi/maven-java-sample-app.git'
            }
        }
        stage('Compile') {
            steps {
                bat 'mvn spring-javaformat:apply'
                bat 'mvn clean compile'
            }
        }
        stage('Build') {
            steps {
                bat 'mvn package'
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    def dockerImage = 'imtiyaz18/maven-java-sample-app'
                    def dockerTag = 'latest'

                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    }
                    bat "docker build -t ${dockerImage}:${dockerTag} ."
                    bat "docker push ${dockerImage}:${dockerTag}"
                }
            }
        }
        stage('Deploy Application') {
            steps {
                script {
                    def dockerImage = 'imtiyaz18/maven-java-sample-app'
                    def dockerTag = 'latest'
                    bat "docker pull ${dockerImage}:${dockerTag}"
                    bat "docker run -d -p 8001:8001 --name maven-app ${dockerImage}:${dockerTag}"
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
