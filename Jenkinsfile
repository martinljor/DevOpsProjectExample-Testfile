pipeline {
    agent { label 'jenk-agent'}
    tools {
        jdk 'Java17'
        maven 'MyMaven'
    }
    environment {
	    APP_NAME = "DevOpsProjectExample-Testfile"
            RELEASE = "1.0.0"
            DOCKER_USER = "thegangg" 
            DOCKER_PASS = 'dockerhub-thegangg' 
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout from SCM"){
            steps{
                git branch: 'main', credentialsId: 'GitHub-martinljor', url: 'https://github.com/martinljor/DevOpsProjectExample-Testfile'
            }
        }
        stage ("Build Application"){
            steps {
                sh "mvn clean package"
            }
        }   
    
        stage ("Test Application"){
            steps {
                sh "mvn test"
            }
        }
        stage ("SonarQube Analysis"){
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'Jenk-Sonar') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("SonarQube Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Jenk-Sonar'
                }	
            }
        }
    }
}
