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
        sh 'rm trufflehog || true'
        sh 'sudo docker run  gesellix/trufflehog https://github.com/dhananjaybhakte/Devsecops.git > trufflehog'
        sh 'cat trufflehog'
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
                        sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.191.221.95:/home/ubuntu/prod/apache-tomcat-9.0.37/webapps/webapp.war'
                }
        }
    }
  }  
}
