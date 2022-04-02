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
