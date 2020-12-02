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
    stage ('Check-Git-Secretts') {
      steps {
        sh 'rm trufflehog || true'
        sh 'sudo docker run  --log-driver json-file --log-opt max-size=1g --log-opt max-file=2 gesellix/trufflehog https://github.com/dhananjaybhakte/Devsecops.git > trufflehog'
        sh 'sudo cat /var/lib/jenkins/workspace/Devsecopsnew/trufflehog'
     }
    }
   
    stage('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
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
                        sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.188.150.171/:/home/ubuntu/prod/apache-tomcat-8.5.60/webapps'
                }
        }
    }
  }  
}
