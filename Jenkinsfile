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
    
    stage ('OWASP Dependency-Check Vulnerabilities') {
            steps {
                dependencyCheck additionalArguments: ''' 
                    -o "./" 
                    -s "./"
                    -f "ALL" 
                    --prettyPrint''', odcInstallation: 'dcheck'

                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
                
            }
        }     
        
    stage ('Scan-Checkout') {
      steps {
      sh 'mvn clean package'
     }
    }


    stage ('Report') {
     steps {
            //sh 'chmod +777 /var/lib/jenkins/workspace/CICD'
            //sh 'sudo cp -r /var/lib/jenkins/workspace/CICD /var/www/html' 
            sh 'sudo cp -r /var/lib/jenkins/workspace/DJScan/./dependency-check-report.html  /var/www/html/DJScan'
       }
    }
   
    
    
    
  /*stage ('OWASP Dependency Check') {
      steps {
         sh 'echo Owasp Dependency Check'
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'       
         sh 'sudo cp -r /var/lib/jenkins/OWASP-Dependency-Check/reports /var/lib/jenkins/workspace/CICD' 
      }
    } */     
    
  }
} 
