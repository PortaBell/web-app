node(""){
 
 def Dansoa = tool name: "maven"
 def Bella = "${Dansoa}/bin/mvn"

  stage("1. Git clone from repo"){
     sh "echo start of git clone"
     git branch: 'main', url: 'https://github.com/PortaBell/web-app.git'
     sh "echo end of git clone"
   }
   
   stage("2. Build from Maven"){
       sh "echo start building from maven"
       sh "${Bella} clean package"
       sh "echo end of building from maven"
   }
   
   stage("3. Code Quality Scan"){
       sh "echo start code scan"
       sh "${Bella} sonar:sonar"
       sh "echo end of code scan"
   }
   
   stage("4. Slack Notification to Developers"){
       sh "echo send notification to Developers"
      slackSend channel: '#ci-cd', message: 'Successful scan of Built Artifacts'
   }
   
   stage("5. Store the Artifact to Nexus"){
       sh "echo start uploading artifact to Nexus"
       sh "${Bella} deploy"
       sh "echo end of upload to Nexus"
   }
   
   stage("6. Deploy to Tomcat in UAT"){
       sh "echo start deploying to Tomcat server in UAT"
       deploy adapters: [tomcat9(credentialsId: 'nick', path: '', url: 'http://35.91.201.141:8080')], contextPath: null, war: 'target/*.war'
       sh "echo end of deploy to Tomcat server in UAT"
   }

   stage("7. Approval by PM"){
      sh " echo Approval required from PM"
      timeout(time:3, unit:'DAYS'){
         input message: "Approval to Deploy to Production Env"
      }
   }

   stage("8. Deploy to Tomcat in Prod Env"){
       sh "echo start deploying to Tomcat server in Prod Env"
      deploy adapters: [tomcat9(credentialsId: 'nick', path: '', url: 'http://35.91.201.141:8080')], contextPath: null, war: 'target/*.war'
       sh "echo end of deploy to Tomcat server in Prod Env"
   }    

   stage("9. Email Notification"){
       sh " echo send successful deployment to tomcat email notification to DevOps Team"
       emailext body: 'Successful deployment to Tomcat server in UAT Environment', subject: 'Successful deployment', to: 'info@jomacsit.com'
    }     
}
