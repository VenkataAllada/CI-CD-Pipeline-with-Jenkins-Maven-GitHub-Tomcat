# 🚀 CI/CD Pipeline with Jenkins, Maven, GitHub & Tomcat

This project demonstrates how to set up a full CI/CD pipeline using **GitHub**, **Jenkins**, **Maven**, and **Apache Tomcat**. It includes both **local** and **remote deployment** scenarios.

---

## 📁 Project Structure

```
sample-maven-app/
├── pom.xml
├── Jenkinsfile
└── src/
    └── main/
        └── webapp/
            └── index.jsp
```

---

## 🔧 Prerequisites

- Java JDK 11+
- Maven
- Git
- Jenkins
- Apache Tomcat (local or remote)
- GitHub account

---

## 🗃️ GitHub Repository Setup

1. Create a new GitHub repo: `sample-maven-app`
2. Push the sample project:

```bash
git init
git remote add origin https://github.com/YOUR_USERNAME/sample-maven-app.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

---

## 🔨 Jenkins Setup

### ✅ Install Plugins

- Git plugin
- Maven Integration
- Deploy to Container
- SSH Plugin (for remote deployment)

### ✅ Configure Tools

Go to: **Manage Jenkins → Global Tool Configuration**
- Add JDK and Maven installation
- Ensure Git is available in PATH

---

## 🏠 Tomcat Setup

### 📍 Local Deployment

1. Download Tomcat: https://tomcat.apache.org
2. Add user to `conf/tomcat-users.xml`:

```xml
<role rolename="manager-script"/>
<user username="admin" password="admin" roles="manager-script"/>
```

3. Start Tomcat:
```bash
./startup.sh
```

### 🌐 Remote Deployment (Optional)

1. Install Tomcat on a remote server
2. Setup SSH access between Jenkins and the server
3. In Jenkins: **Manage Jenkins → Configure System → Publish Over SSH**

---

## 🛠️ Jenkins Pipeline Job

Create a new **Pipeline** job in Jenkins and use the following script:

### `Jenkinsfile`

```groovy
pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/YOUR_USERNAME/sample-maven-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Deploy to Tomcat (Local)') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-creds',
                    path: '', url: 'http://localhost:8080')],
                    contextPath: 'sampleapp',
                    war: '**/target/*.war'
            }
        }

        stage('Deploy to Tomcat (Remote)') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'remote-tomcat',
                    transfers: [sshTransfer(sourceFiles: '**/target/*.war',
                    removePrefix: 'target', remoteDirectory: '/opt/tomcat/webapps')],
                    usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
            }
        }
    }
}
```

---

## 🔁 GitHub Webhook (Optional)

Trigger Jenkins build on code push:

1. Go to GitHub → Repo Settings → Webhooks → Add Webhook
2. Set:
   - Payload URL: `http://<jenkins-host>:8080/github-webhook/`
   - Content Type: `application/json`
   - Event: `Just the push event`

---

## 🚀 Access the Application

- **Local Tomcat**: [http://localhost:8080/sampleapp](http://localhost:8080/sampleapp)
- **Remote Tomcat**: [http://<remote-server>:8080/sampleapp](http://<remote-server>:8080/sampleapp)

---

## 🧠 Tips

- Make sure Jenkins has access to Maven and Tomcat
- Use Jenkins **credentials manager** for `tomcat-creds` and `remote-tomcat`
- Test the WAR locally with `mvn clean package` before pushing

---

## 📘 License

This project is licensed under the MIT License.

---

## 🙋 Support

For issues, please open an issue in the GitHub repo or contact `support@example.com`
