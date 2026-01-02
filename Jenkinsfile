pipeline {
  agent any

  environment {
    TOMCAT_HOME = "/home/ubuntu/apache-tomcat-11.0.15"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
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
          sh 'mvn sonar:sonar -Dsonar.projectKey=hello-java'
        }
      }
    }
    stage('Quality Gate') {
  steps {
    timeout(time: 1, unit: 'MINUTES') {
      waitForQualityGate abortPipeline: true
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
