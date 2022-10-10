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
    }

}