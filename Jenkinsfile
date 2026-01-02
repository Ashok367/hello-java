pipeline {
  agent any

  environment {
    TOMCAT_HOME = "/home/ubuntu/apache-tomcat-11.0.15"
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/Ashok367/hello-java.git'
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh 'mvn sonar:sonar'
        }
      }
    }

    stage('Deploy to Tomcat') {
      steps {
        sh '''
          rm -rf $TOMCAT_HOME/webapps/hello-1.0*
          cp target/hello-1.0.war $TOMCAT_HOME/webapps/
          $TOMCAT_HOME/bin/shutdown.sh || true
          sleep 3
          $TOMCAT_HOME/bin/startup.sh
        '''
      }
    }
  }
}
