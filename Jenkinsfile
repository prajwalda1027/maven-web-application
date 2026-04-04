pipeline {
    agent any


    tools {
        maven "maven3.9.14"
    }
options {
  timestamps()
  buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '2', daysToKeepStr: '', numToKeepStr: '2')
}
triggers {
  pollSCM 'H/2 * * * *'
}

    stages {

        stage('Checkout from GitHub') {
            steps {
                git credentialsId: 'github', url: 'https://github.com/prajwalda1027/maven-web-application.git'
            }
        }

        stage('Build the package') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('SonarQube report') {
            steps {
                sh "mvn sonar:sonar"
            }
        }

        stage('Deploy artifact in Nexus') {
            steps {
                sh "mvn deploy"
            }
        }

        stage('Deploy in Tomcat') {
            steps {
                sshagent(['3ad84d4f-f957-4b71-8837-4293726cda73']) {
                    sh '''
                        ls -l target/
                        scp -o StrictHostKeyChecking=no target/*.war ubuntu@34.239.124.105:/opt/tomcat/webapps
                    '''
                }
            }
        }
    }
   
    }
}
}
