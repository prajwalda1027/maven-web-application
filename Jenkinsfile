node {
    def mavenHome = tool name: 'maven3.9.14'
    echo " the build number : ${env.BUILD_NUMBER}"
    echo "the JOB_NAME is : ${env.JOB_NAME}"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))])
    properties([pipelineTriggers([pollSCM('* * * * *')])])
    parameters {
  choice choices: ['master', 'dev', 'test', 'QA'], description: 'Branchname select', name: 'Branchname'
}
    stage('Checkout') {
        git branch: "${params.Branchname}", credentialsId: 'github', url: 'https://github.com/prajwalda1027/maven-web-application.git'
    }

    stage('Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage("Sonarqube report") {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }

    stage('nexus') {
        sh "${mavenHome}/bin/mvn deploy -s /var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/maven3.9.14/conf/settings.xml"
    }

    stage('tomcat'){
        sshagent(['5460b8ff-1693-4b9d-ace1-4d454de29c72']) {
            // Changed 'ec2-user' to 'ubuntu'
            sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ubuntu@54.237.169.220:/opt/tomcat/webapps/"
        }
    }
}
