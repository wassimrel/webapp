pipeline {
  agent any 
  tools {
    maven 'maven'
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


    stage ('Check Git Secrets') {
      steps {
    	sh 'rm trufflehog || true'
	sh 'docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
	sh 'cat trufflehog'
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
    
    stage ('Deploy to Tomcat') {
	    steps {
		  sshagent(['tomcat']) {
		    sh '''
		       scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.224.110.160:/home/ubuntu
		       ssh -o StrictHostKeyChecking=no ubuntu@18.224.110.160 'cp -r /home/ubuntu/*.war /opt/tomcat/webapps/webapp.war'
		    '''
		  }
	    }  
    }

	  
	  
  }
}
