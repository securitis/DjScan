pipeline {
  agent any 
            
  tools {
    maven 'maven'
  }
        
  environment {
    Snyk = 'Snyk'
    Trivy = 'Trivy'
    Audit = 'Audit'
  }
  
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                  
            ''' 
      }
    } 
    
  stage ('OWASP Dependency Check') {
      steps {
         sh 'echo Owasp Dependency Check'
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'       
         sh 'sudo cp -r /var/lib/jenkins/OWASP-Dependency-Check/reports /var/lib/jenkins/workspace/CICD' 
      }
    }   
     
   
   stage ('Build') {
      steps {
      sh 'mvn clean package'
     }
    }


    stage ('Deploy') {
     steps {
            sh 'chmod +777 /var/lib/jenkins/workspace/CICD'
            sh 'sudo cp -r /var/lib/jenkins/workspace/CICD /var/www/html' 
            //sh 'chmod +777 /var/lib/jenkins/workspace/CICD/target/WebApp'
            //sh 'ls /var/www/html'
            //sh 'sudo cp -r /var/lib/jenkins/OWASP-Dependency-Check/reports /var/www/html'
       }
    }
      
    
  }
} 
