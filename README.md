Java CI/CD Pipeline using Jenkins, SonarQube, and Tomcat on AWS EC2

Project Overview
This project demonstrates a complete CI/CD (Continuous Integration and Continuous Deployment) pipeline for a Java web application using modern DevOps tools.  

The application is built using Java Servlets, version-controlled in GitHub, automatically built and analyzed using Jenkins and SonarQube, and deployed to Apache Tomcat running on an Ubuntu-based AWS EC2 instance.

The goal of this project is to show end-to-end automation, starting from code commit to production deployment.

---

Tools & Technologies Used
- Java 21 – Application development
- Maven – Build and dependency management
- Git & GitHub – Source code management
- Jenkins – CI/CD automation
- SonarQube (Docker) – Static code analysis
- Apache Tomcat 11 – Application server
- Docker – Running SonarQube
- AWS EC2 (Ubuntu) – Infrastructure

---

CI/CD Workflow

Developer Commit
↓
GitHub Webhook
↓
Jenkins Pipeline
↓
Maven Build
↓
SonarQube Code Analysis
↓
Deploy to Apache Tomcat


This workflow ensures that every code change is automatically built, analyzed, and deployed without manual intervention.


Create and Push Java Application to GitHub**
- Created a Hello World Java Servlet
- Configured Maven to build a WAR file
- Initialized a Git repository and pushed the source code to GitHub

---

Manual Deployment to Apache Tomcat
- Installed Apache Tomcat on Ubuntu EC2
- Changed Tomcat port to `8081`
- Manually deployed the WAR file by copying it to the `webapps` directory
- Verified the application through the browser

---

CI/CD Automation using Jenkins
- Installed and configured Jenkins on EC2
- Configured Jenkins to pull source code from GitHub
- Integrated **SonarQube** for code quality analysis
- Automated deployment of the application to Tomcat using Jenkins Pipeline
- Enabled GitHub Webhook to trigger Jenkins automatically on code push

---

Jenkins Pipeline Stages

1. Checkout
   - Jenkins pulls the latest code from GitHub

2. Build
   - Maven compiles the application and creates a WAR file

3. SonarQube Analysis
   - Jenkins sends code to SonarQube for static analysis
   - Code quality metrics such as bugs, vulnerabilities, and code smells are evaluated

4. Deploy to Tomcat
   - WAR file is copied to Tomcat `webapps` directory
   - Tomcat is restarted automatically

---

EC2 Security Group Configuration

The following inbound ports were enabled:

| Port | Service |
|----|-------|
| 22 | SSH |
| 8080 | Jenkins |
| 8081 | Tomcat |
| 9000 | SonarQube |

---

Application Access

- Jenkins UI 
http://<EC2-IP>:8080


- SonarQube UI
http://<EC2-IP>:9000

Deployed Application to Tomcat 
http://<EC2-IP>:8081/hello-1.0/hello


Key Learnings
- Understood how CI/CD pipelines work in real-world scenarios
- Implemented automated builds and deployments using Jenkins
- Integrated static code analysis using SonarQube
- Deployed Java applications on Apache Tomcat
- Gained hands-on experience with AWS EC2 and Linux administration


I built a Java CI/CD pipeline where GitHub triggers Jenkins automatically using a webhook. Jenkins builds the application using Maven, runs SonarQube analysis, and deploys the application to Apache Tomcat on an EC2 instance.”

---

