pipeline 
{ 
  tools
  {
    maven "Maven"
  }
  agent any
    stages
    {
        stage ('Initialize') 
        {
          steps {
          sh '''
                      echo "PATH = ${PATH}"
                      echo "M2_HOME = ${M2_HOME}"
              ''' 
          }
        }
       stage ('Check-Git-Secrets') {
        steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/benson4u2c/webapp.git > trufflehog'
        sh 'cat trufflehog'
        }
       }
      
      stage ('Source Composition Analysis') {
        steps {
         sh 'rm owasp* || true'
         sh 'wget "https://github.com/benson4u2c/webapp/blob/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
        }
      }
      
      stage ('SAST') {
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
         stage('Manual Approval'){
            steps{
                input 'Deploying to Stage server'
            }
         }

       stage ('Deploy-To-Tomcat') {
              steps {
             sshagent(['tomcat']) {
                  sh 'scp -o StrictHostKeyChecking=no target/*.war root@192.168.0.153:/home/PROD/apache-tomcat-8.5.77/webapps/webapp.war'
                }      
             }       
        }
       stage ('DAST') {
          steps {
            sshagent(['zap']) {
              sh 'ssh -o  StrictHostKeyChecking=no root@192.168.0.153 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.0.153:8080/webapp/" || true'
            }
          }
        }
      
      
      
    }
    
    
}
