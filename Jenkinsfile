pipeline {
    agent { label 'Slave'}
    environment {
        WAR_PATH= "$WORKSPACE/target/*.war"
        IMAGE_NAME = "sanjeetkr/java_webapp"
        IMAGE_TAG = "$BUILD_NUMBER"
    }
    options {
        buildDiscarder(logRotator(numToKeepStr : '5'))
        retry(2)
    }
    tools {
        maven 'Maven_local'
    }
    triggers {
        pollSCM('* * * * *')
    }
    stages {
        stage('Workspace Clean'){
            steps{
                script{
                    cleanWs()
                    sh "echo cleaned up workspace for ${JOB_NAME} at ${NODE_NAME}"
                }
            }
        }
        stage('SCM checkout'){
            steps {
                git branch: 'main', url: 'https://github.com/sanjeetcalgary/CICD_Deployment.git'
            }
        }
        stage('Generate the artifact'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Building container image') {
            steps{
                sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
                sh "docker build -t $IMAGE_NAME:latest ."
            }
        }
        stage('Docker login'){
            steps{
                withCredentials([string(credentialsId: 'dockerpassword', variable: 'dockerpasswd')]) {
                    sh "docker login -u sanjeetkr -p $dockerpasswd"
                }
            }
        }
         stage('Push to dockerhub'){
            steps{
                sh "docker push $IMAGE_NAME:$IMAGE_TAG"
                sh "docker push $IMAGE_NAME:latest"
            }
        }
        stage("SSH Into k8s Server") {
            def remote = [:]
            remote.name = 'K8S master'
            remote.host = '10.0.0.112'
            remote.user = 'master'
            remote.password = 'admin'
            remote.allowAnyHosts = true
        } 
    }

}