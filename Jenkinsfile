pipeline {
   agent any

   tools {
       maven "Maven-3.9.11"
   }

   stages {

       stage('Git Checkout') {
           steps {
               notifyBuild('STARTED')
               git branch: 'dev', url: 'https://github.com/ravitejaogety/maven-webapplication-project-kkfunda.git'
           }
       }

       stage('Compile') {
           steps {
               sh "mvn compile"
           }
       }

       stage('Maven Build') {
           steps {
               sh "mvn clean package"
           }
       }
       stage('SonarQube') {
           steps {
               sh "mvn sonar:sonar"
           }
       }
       stage('Nexus') {
           steps {
               sh "mvn clean deploy"
           }
       }
       stage('Deploy to Tomcat') {
           steps {
               sh """
           curl -u ravi:password \
           --upload-file /var/lib/jenkins/workspace/ravi-declarativeway-pipeline-dev/target/maven-web-application.war \
           "http://13.204.64.180:8080/manager/text/deploy?path=/maven-web-application&update=true"
           """

           }
       }

   }
    post {
  success {

    script
    {
     notifyBuild(currentBuild.result)
    }
    
  }
  failure {

  script
  {
    notifyBuild(currentBuild.result)

  }
   
  }
}

}

// Notification method
def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    switch (buildStatus) {
        case 'STARTED':
            colorCode = '#FFFF00' // Yellow
            break
        case 'SUCCESS':
            colorCode = '#00FF00' // Green
            break
        default:
            colorCode = '#FF0000' // Red
    }

    slackSend (color: colorCode, message: summary, channel: '#jio-dev')
    slackSend (color: colorCode, message: summary, channel: '#jio-project')
    slackSend (color: colorCode, message: summary, channel: '#ravi')
}
