pipeline {
    agent { label 'jenk-agent'}
    tools {
        jdk 'Java17'
        maven 'MyMaven'
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
    stage("POM File") {
        steps {
            script {
                echo 'Get Library'
                sh 'curl -O http://link.to.my.jar/my-jar/1.0/my-jar.jar'

                echo 'create POM'
                sh 'echo "<project xmlns=\\"http://maven.apache.org/POM/4.0.0\\" xmlns:xsi=\\"http://www.w3.org/2001/XMLSchema-instance\\" xsi:schemaLocation=\\"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xs\\"> <groupId>com.xx.xml</groupId> <artifactId>my-artifact</artifactId> <version>1.0</version> </project>" > my-jar.pom'

                echo 'install POM'
                sh 'mvn install:install-file -Dfile=my-jar.jar -DpomFile=my-jar.pom'

                echo 'install mvn'
                sh 'mvn -B -U -X clean install'
            }
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
    }
}
