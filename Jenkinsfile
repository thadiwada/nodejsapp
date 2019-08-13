node {
    stage('Code Checkout') { // for display purposes
     echo 'Checout Code and clone it inside jenkins workspace.'
     git 'https://github.com/thadiwada/nodejsapp.git'
   }
   stage('Build Test & Package') {
      echo 'Build the package'
      withMaven(jdk: 'JDK-1.8', maven: 'Maven-3.6.1') {
       sh 'mvn clean install'
     }
   }
   stage('SonarScan') 
   withSonarQubeEnv('Sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
                    sh "cat .scannerwork/report-task.txt"
                    def props = readProperties  file: '.scannerwork/report-task.txt'
                    echo "properties=${props}"
                    def sonarServerUrl=props['serverUrl']
                    def ceTaskUrl= props['ceTaskUrl']
                    def ceTask
                    timeout(time: 1, unit: 'MINUTES') {
                        waitUntil {
                            def response = httpRequest ceTaskUrl
                            ceTask = readJSON text: response.content
                            echo ceTask.toString()
                            return "SUCCESS".equals(ceTask["task"]["status"])
                        }
                    }
                    def response2 = httpRequest url : sonarServerUrl + "/api/qualitygates/project_status?analysisId=" + ceTask["task"]["analysisId"], authentication: 'jenkins_scanner'
                    def qualitygate =  readJSON text: response2.content
                    echo qualitygate.toString()
                    if ("ERROR".equals(qualitygate["projectStatus"]["status"])) {
                        error  "Quality Gate failure"
                    }
                }
   
   
   
 
   stage('Artifacts') {
       echo 'package the project artifacts..'
       withMaven(jdk: 'JDK-1.8', maven: 'Maven-3.6.1') {
       sh 'mvn package'
     }
   
   }
   stage('Deploy to Dev'){
       echo 'Deploy to Dev environment'
   }
   stage('Deploy to Test'){
       echo 'Deploy to Test environment'
   }
      stage('Test Automation'){
       echo 'Deploy to Dev environment'
   }
   
}
