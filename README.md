Java CI/CD Pipeline with Jenkins, SonarQube, and Tomcat on AWS EC2

📌 Project Overview
This project demonstrates an end-to-end CI/CD pipeline for a Java web application using:

- GitHub for source control
- Jenkins for CI/CD automation
- SonarQube for code quality analysis
- Apache Tomcat for application deployment
- Docker for running SonarQube
- AWS EC2 (Ubuntu) as the infrastructure

The pipeline automatically builds, analyzes, and deploys the application whenever code is pushed to GitHub.

---

✅ Tasks Completed

- ✔ Created a Hello World Java web application
- ✔ Pushed source code to GitHub
- ✔ Installed Apache Tomcat
- ✔ Manually deployed the Java application to Tomcat
- ✔ Automated build and deployment using Jenkins
- ✔ Integrated SonarQube for static code analysis
- ✔ Automated deployment to Tomcat via Jenkins

---

🔁 Overall Flow



EC2 Ubuntu
├─ Install Java + Maven + Git
├─ Create Java Web App (Servlet)
├─ Build locally
├─ Push code to GitHub (Task 5)
├─ Install Tomcat
├─ Deploy app manually to Tomcat (Task 6)
├─ Install Jenkins
├─ Install Docker + SonarQube
├─ Jenkins pulls code from GitHub
├─ Jenkins builds + Sonar scan
└─ Jenkins deploys to Tomcat (Task 7)


---

🔐 EC2 Security Group Configuration

Allow inbound traffic from your IP:

| Port | Purpose |
|-----|--------|
| 22 | SSH |
| 8081 | Tomcat |
| 8080 | Jenkins |
| 9000 | SonarQube |

---

Step 1: Install Java 21, Maven, and Git

```bash
sudo apt update -y
sudo apt install -y openjdk-21-jdk maven git curl unzip


Verify installation:

java -version
mvn -v
git --version

Step 2: Create Java Web Application (Servlet)
2.1 Create directories
cd ~
mkdir -p hello-java/src/main/java/com/devops
mkdir -p hello-java/src/main/webapp/WEB-INF
cd hello-java

2.2 Create Servlet

File: src/main/java/com/devops/HelloServlet.java

package com.devops;

import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        resp.setContentType("text/plain");
        resp.getWriter().println("Hello World from Tomcat!");
    }
}

2.3 Create web.xml

File: src/main/webapp/WEB-INF/web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee" version="6.1">
</web-app>

2.4 Create pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.devops</groupId>
  <artifactId>hello</artifactId>
  <version>1.0</version>
  <packaging>war</packaging>

  <properties>
    <maven.compiler.release>21</maven.compiler.release>
  </properties>

  <dependencies>
    <dependency>
      <groupId>jakarta.servlet</groupId>
      <artifactId>jakarta.servlet-api</artifactId>
      <version>6.1.0</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.11.0</version>
        <configuration>
          <release>21</release>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.5.1</version>
        <configuration>
          <failOnMissingWebXml>false</failOnMissingWebXml>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

2.5 Build application
mvn clean package
ls target/hello-1.0.war

Step 3: Push Code to GitHub (Task 5)
git init
git add .
git commit -m "Hello World Java Web App"
git branch -M main
git remote add origin https://github.com/<YOUR_USERNAME>/hello-java.git
git push -u origin main


🔑 Use GitHub Personal Access Token as password.

Step 4: Install Apache Tomcat (Latest)
cd ~
wget https://downloads.apache.org/tomcat/tomcat-11/v11.0.15/bin/apache-tomcat-11.0.15.tar.gz
tar -xvzf apache-tomcat-11.0.15.tar.gz
chmod +x apache-tomcat-11.0.15/bin/*.sh

Change Tomcat port to 8081

Edit apache-tomcat-11.0.15/conf/server.xml:

port="8081"


Start Tomcat:

apache-tomcat-11.0.15/bin/startup.sh


Access:

http://<EC2-IP>:8081

Step 5: Manual Deployment to Tomcat (Task 6)
cp target/hello-1.0.war apache-tomcat-11.0.15/webapps/
apache-tomcat-11.0.15/bin/shutdown.sh
apache-tomcat-11.0.15/bin/startup.sh


Verify:

http://<EC2-IP>:8081/hello-1.0/hello

Step 6: Install Jenkins
sudo apt install -y fontconfig
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update -y
sudo apt install -y jenkins
sudo systemctl enable --now jenkins


Access Jenkins:

http://<EC2-IP>:8080


Unlock:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Step 7: Install Docker & SonarQube
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker ubuntu
newgrp docker


Run SonarQube:

docker run -d --name sonarqube -p 9000:9000 sonarqube:lts


Access:

http://<EC2-IP>:9000


Create a SonarQube token for Jenkins.

Step 8: Jenkins Pipeline (Task 7)
Give Jenkins permissions
sudo chown -R jenkins:jenkins /home/ubuntu/hello-java
sudo chown -R jenkins:jenkins /home/ubuntu/apache-tomcat-11.0.15

Jenkinsfile
pipeline {
  agent any
  environment {
    TOMCAT_HOME = "/home/ubuntu/apache-tomcat-11.0.15"
  }
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/<YOUR_USERNAME>/hello-java.git'
      }
    }
    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
    }
    stage('SonarQube') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh 'mvn sonar:sonar'
        }
      }
    }
    stage('Deploy to Tomcat') {
      steps {
        sh '''
          cp target/hello-1.0.war $TOMCAT_HOME/webapps/
          $TOMCAT_HOME/bin/shutdown.sh || true
          sleep 2
          $TOMCAT_HOME/bin/startup.sh
        '''
      }
    }
  }
}


Commit and push:

git add Jenkinsfile
git commit -m "Add Jenkins pipeline"
git push

✅ Final Verification
http://<EC2-IP>:8081/hello-1.0/hello

🎯 Key Learnings

Implemented CI/CD using Jenkins

Integrated SonarQube for quality checks

Automated deployment to Tomcat

Used AWS EC2 with Ubuntu for infrastructure
