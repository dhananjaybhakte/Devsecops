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
        sh 'sudo cat /var/lib/jenkins/workspace/Devsecops/trufflehog'
     }
    }
    stage ('Source Composition Analysis') {
      steps {
        sh 'rm trufflehog || true'
        sh 'sudo rm /var/lib/jenkins/workspace/Devsecops/owasp-dependency-check.sh'
        sh 'sudo wget "https://github.com/dhananjaybhakte/Devsecops/blob/master/owasp-dependency-check.sh"'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'sudo bash /var/lib/jenkins/workspace/Devsecops/owasp-dependency-check.sh'
        sh 'sudo cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
      }
    }
    stage('SAST') {
      steps {
        withSonarQubeEnv('devopssecure') {
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
                        sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.191.221.95:/home/ubuntu/prod/apache-tomcat-9.0.37/webapps/webapp.war'
                }
        }
    }
  }  
}
