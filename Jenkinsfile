pipeline {
 agent any
 tools {
   maven 'maven'
 }
 stages {
   stage ('Initialize') {
     steps {
       sh '''
                 echo "PATH = $(PATH)"
                 echo "M2_HOME = $(M2_HOME)"
           '''
      }
    }
    stage ('Check-Git-Secrets') {
      steps {
        sh 'sudo rm trufflehog || true'
        sh 'sudo -n docker run  gasellix/trufflehog --json https://github.com/devopssecuree/webapp.git > trufflehog'
        sh 'sudo cat trufflehog'
     }
    }
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
    }
    }
    stage ('Deploy-To-Tomcat') {
        steps {
                sshagent(['tomcat']) {
                        sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.224.29.54:/home/ubuntu/prod/apache-tomcat-9.0.37/webapps/webapp.war'
                }
        }
    }
  }  
}
