pipeline {
    agent { label 'jenk-agent'}
    tools {
        jdk 'Java17'
        maven 'MyMaven'
    }
    environment {
	    APP_NAME = "devopsprojectexample-testfile"
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
        stage("B&P Docker-Image") {
            steps {
              script {
                docker.withRegistry('',DOCKER_PASS) {
                    docker_image = docker.build "${IMAGE_NAME}"
                }

                    docker.withRegistry('',DOCKER_PASS) {
                    docker_image.push("${IMAGE_TAG}")
                    docker_image.push('latest')
                }
                }
              }
            }
            
        stage("Trivy-Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ashfaque9x/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }
    }
}
