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
    }
    
    
}
