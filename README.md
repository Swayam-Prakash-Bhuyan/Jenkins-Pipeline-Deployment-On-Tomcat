# Jenkins Project Setup

## Install Jenkins

1. Go to the Jenkins website and download the Jenkins WAR file:

wget https://updates.jenkins.io/download/war/2.387.3/jenkins.war


2. Install JDK:
sudo apt update -y
sudo apt install openjdk-11-jre -y


3. Start Jenkins and obtain the initial admin password:
java -jar jenkins.war -httpPort=8082


## Jenkins Configuration

### Manage Plugins

- Navigate to **Manage Jenkins** > **Manage Plugins** > **Available**.
- Download plugins for tools such as Eclipse Temurin Installer, OpenJDK Native Plugin, OWASP Dependency Check, Docker, Docker Pipeline, Docker Build Step, and CloudBees Docker Build and Publish.

### Configure Global Tools

- Go to **Manage Jenkins** > **Global Tool Configuration**.
- Configure installed tools like JDK 11, Maven and OWASP Dependency Check.

### Configure System

- Go to **Manage Jenkins** > **Configure System**.
- Configure integration with servers like SonarQube or Nexus.

### Manage Nodes and Cloud

- Go to **Manage Jenkins** > **Manage Nodes and Cloud**.
- Configure the nodes and VMs to be used by Jenkins.

### Configure Global Security

- Go to **Manage Jenkins** > **Configure Global Security**.
- Adjust the security settings as per your requirements.

### Credentials

- Go to **Manage Jenkins** > **Manage Credentials**.
- Add necessary credentials for authenticating with external systems.

### Manage Users

- Go to **Manage Jenkins** > **Manage Users**.
- Manage Jenkins user accounts.

## Create Jobs and Projects

### Create a FreeStyle Project

1. Specify the project name.
2. Configure source code management with Git, providing the project's URL.
3. Add build steps, e.g., invoking Maven targets like clean compile and clean package.

## Install Apache Tomcat

1. Change to the `/opt` directory:
cd /opt


2. Download Apache Tomcat:
sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.65/bin/apache-tomcat-9.0.65.tar.gz


3. Extract the downloaded archive:
sudo tar -xvf apache-tomcat-9.0.65.tar.gz


### Configure Tomcat User Roles

1. Go to the Tomcat configuration directory:
cd /opt/apache-tomcat-9.0.65/conf


2. Open the `tomcat-users.xml` file for editing:
sudo vi tomcat-users.xml


3. Add the following line at the end of the file:
```xml
<user username="admin" password="admin1234" roles="admin-gui, manager-gui"/>
Create Symbolic Links
Create a symbolic link for starting Tomcat:


sudo ln -s /opt/apache-tomcat-9.0.65/bin/startup.sh /usr/bin/startTomcat
Create a symbolic link for stopping Tomcat:


sudo ln -s /opt/apache-tomcat-9.0.65/bin/shutdown.sh /usr/bin/stopTomcat
Edit context.xml Files
Open the context.xml file for both the manager and host-manager applications:


sudo vi /opt/apache-tomcat-9.0.65/webapps/manager/META-INF/context.xml
sudo vi /opt/apache-tomcat-9.0.65/webapps/host-manager/META-INF/context.xml
Comment out the following line in both files:


<!-- Valve className="org.apache.catalina.valves.RemoteAddrValve"
  allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Start and Stop Tomcat
Stop Tomcat:

sudo stopTomcat
Start Tomcat:

sudo startTomcat
Create Jenkins Pipeline
Create a pipeline project and define the following declarative pipeline:

pipeline {
  agent any
  
  tools {
    jdk 'jdk11'
    maven 'maven3'
  }
  
  stages {
    stage('Git Checkout') {
      steps {
        git changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/secretsanta-generator.git'
      }
    }
    
    stage('Compile') {
      steps {
        sh "mvn clean compile"
      }
    }
    
    stage('Build') {
      steps {
        sh "mvn clean package"
      }
    }
    
    stage('Deploy') {
      steps {
        sh "sudo cp target/secretsanta-0.0.1-SNAPSHOT.jar /opt/apache-tomcat-9.0.65/webapps"
      }
    }
  }
}

### Adjust Apache Tomcat Permissions

chmod -R 757 apache-tomcat-9.0.65
Run the Jenkins Pipeline


### Save and execute the pipeline.

Please note that the commands and configurations provided in the guide may need to be adapted based on your specific setup and requirements.


