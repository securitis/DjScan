pipeline {
  agent any 
     
 // options //{
   //     ansiColor('xterm')
    //}
  
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
    
 stage ('Check-Git-Secrets') {
      steps {
        //sh 'sudo docker run raphaelareya/gitleaks:latest -r https://github.intra.fcagroup.com/t0996su/CICD.git'
        // sh 'sshpass -p Stellantis01 ssh devuser@10.109.137.30 "exit 0" '
        
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }  
    
    stage ('SCA') {      
    parallel {
      
      
      stage ('OWASP Dependency Check') {
      steps {
         sh 'echo Owasp Dependency Check'
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'       
         sh 'sudo cp -r /var/lib/jenkins/OWASP-Dependency-Check/reports /var/lib/jenkins/workspace/CICD' 
         sh '''
                export ARCHERY_HOST=http://localhost:8002
            '''
         publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: 'RcovReport'])
      }
    }
      
        stage ('Snyk'){
         
          
          steps {
            sh 'echo synk'
      }
    }
      
     stage ('Trivy - Git repo scan'){
              
       steps {
         sh 'docker run aquasec/trivy:0.18.3 vulnerables/phpldapadmin-remote-dump'
         sh 'docker run aquasec/trivy:0.18.3 repo https://github.intra.fcagroup.com/t0996su/CICD.git'
       }
     }
  }    
}
    
stage ('IBM App Scan Source') {
         steps {
      appscan application: '4130440a-8227-4b5f-b846-e4ef704931fb', credentials: 'appscan', name: 'CICDDemoAuguest ', scanner: static_analyzer(hasOptions: false, target: '/var/lib/jenkins/workspace/CICD'), type: 'Static Analyzer'
      } 
    }
      
 /*start 
 stage ('SAST Scan') {
     parallel {
    
      stage ('Trivy - Image Scan') {
            steps {
              sh 'docker run aquasec/trivy:0.18.3 image vulnerables/web-dvwa:latest" '
    }
   }
   
     stage ('Qualys - Image Scan') {
          steps {
   getImageVulnsFromQualys apiServer: 'https://qualysapi.qualys.com', credentialsId: 'Qualys', imageIds: 'vulnerables/web-dvwa:latest,', pollingInterval: '30', proxyCredentialsId: 'Qualys', proxyPort: 9080, proxyServer: 'aiproxy.appl.chrysler.com', useLocalConfig: true, useProxy: true, vulnsTimeout: '600'
     }
    }  
  
  }  
} 
 end  */
     
   stage ('Build') {
      steps {
      sh 'mvn clean package'
     }
    }

    stage ('Artifact Analysis - Trivy') {
        parallel {
          
          stage ('Container Scan') {
            steps {
              sh 'docker run aquasec/trivy:0.18.3 vulnerables/phpldapadmin-remote-dump'
              }
             }
             
            stage ('Audit - Docker Bench') {                 
             steps {
                    sh 'echo Audit'
                   //sh 'cd ~/docker-bench-security/; ./docker-bench-security.sh'
              }
       } 
  }
 }
 
    stage ('Deploy') {
     steps {
            sh 'chmod +777 /var/lib/jenkins/workspace/CICD/target/WebApp'
            sh 'sudo cp -r /var/lib/jenkins/workspace/CICD/target/WebApp /var/www/html' 
            sh 'ls /var/www/html'
            sh 'sudo cp -r /var/lib/jenkins/OWASP-Dependency-Check/reports /var/www/html'
       }
    }

    stage ('DAST Scan') {
      parallel {
        stage ('Arachni') {
      steps {
        sh 'echo arachni'
       /* sh 'sudo docker exec c2406913789c52e3dc69b680b93f60dc97d64b825f0948f2afbe2a9c95a61678 bash /arachni/bin/./arachni http://10.109.137.30:80/WebApp/'
        sh 'echo REPORTS SAVED in /arachni Folder' */
        }
    }
      stage ('IBMApp Scan Dynamic') {
      steps {
      appscan application: '4130440a-8227-4b5f-b846-e4ef704931fb', credentials: 'appscan', name: 'CICDDemoAuguest_DynScan', scanner: dynamic_analyzer(hasOptions: false, optimization: 'Fast', scanType: 'Staging', target: 'http://altoromutual.com:8080/login.jsp'), type: 'Dynamic Analyzer'
     } 
    }  
   }
  }
    stage ('Infra Scan') {
       parallel {
        stage ('OpenVAS') {
          steps {
            sh 'echo https://10.109.137.30/omp?cmd=get_tasks&token=a96dba21-5731-4e03-a0c1-f2f6320187d3'
           }
         }
       stage ('QualysGuard') {
           steps {
                        sh 'echo Qulays Temp'
   /* qualysVulnerabilityAnalyzer apiServer: ' https://qualysapi.qualys.com', credsId: 'Qualys', hostIp: '10.109.137.30', network: 'ACCESS_FORBIDDEN', optionProfile: 'Stellantis Full Scan', platform: 'US_PLATFORM_1', pollingInterval: '2', proxyCredentialsId: 'Qualys', proxyPort: 9080, proxyServer: 'aiproxy.appl.chrysler.com', scanName: 'Test123', scannerName: 'N_AZURE_1', useHost: true, useProxy: true, vulnsTimeout: '60*100000' */
    
    }
        } 
     }
   }
   
       stage ('Health') {
       parallel {
       
        stage ('Jenkins|Monitor') {
          steps {
            sh 'echo http://10.109.137.30:8080/monitoring'
           }
         }
         
         stage ('Splunk') {
           steps {
           sh 'echo https://shcspsp02.shdc.chrysler.com:8443/en-US/app/splunk_app_jenkins/build'
         }
        } 
        
       stage ('Notification-Hangouts') {
           steps {
             googlechatnotification message: 'Build Success!!!', notifySuccess: true, url: 'https://mail.google.com/chat/u/0/#chat/space/AAAAIdtxLQw'
            //hangoutsNotify message: "The Build was Success !!!",token: "5Q0YJlSzAaRfDC9cbzHvYTZNp",threadByJob: false
            }
        } 
     }
   }
   
    
  }
} 



