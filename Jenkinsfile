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
          sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=hello-java'

        }
      }
    }
    stage('Quality Gate') {
  steps {
    timeout(time: 10, unit: 'MINUTES') {
      waitForQualityGate abortPipeline: false
    }
  }
}


  stage('Deploy to Tomcat') {
  steps {
    sh '''
      echo "Stopping Tomcat..."
      $TOMCAT_HOME/bin/shutdown.sh || true
      sleep 3

      echo "Removing old deployment..."
      rm -rf $TOMCAT_HOME/webapps/hello-1.0*

      echo "Deploying new WAR..."
      cp target/hello-1.0.war $TOMCAT_HOME/webapps/

      echo "Starting Tomcat..."
      $TOMCAT_HOME/bin/startup.sh
    '''
  }
}


  }
}
