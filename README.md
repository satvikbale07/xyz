[README.md](https://github.com/user-attachments/files/35013047/README.md)
# xyz# DevOps Practicals

A beginner-friendly practical journal covering the 10 DevOps experiments in the syllabus. Commands and examples are intended for a local learning environment. Adjust paths, versions, ports, and credentials as needed.


## Contents
1. [Git and Version Control](#1-git-and-version-control)
2. [Jenkins Installation and CI](#2-jenkins-installation-and-continuous-integration)
3. [Jenkins with Maven](#3-jenkins-with-maven-automated-build-pipeline)
4. [Docker and Dockerfile](#4-docker-and-dockerfile)
5. [Docker Compose](#5-docker-compose-multi-container-application)
6. [Ansible Playbooks](#6-ansible-playbooks)
7. [Kubernetes Deployment](#7-kubernetes-deployment)
8. [Kubernetes Scaling, Rolling Updates and Rollbacks](#8-kubernetes-scaling-rolling-updates-and-rollbacks)
9. [Prometheus and Grafana](#9-prometheus-and-grafana-monitoring)
10. [ELK Stack](#10-elk-stack-log-monitoring)

---

## 1. Git and Version Control

### Aim
To understand version control and practice creating repositories, commits, branches, merging, and resolving merge conflicts using Git.

### Requirements
- Git installed: https://git-scm.com/downloads
- Terminal, PowerShell, or Git Bash

### Steps

**1. Check Git installation**
```bash
git --version
```

**2. Configure your identity** (use your own details)
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

**3. Create a project and initialize a repository**
```bash
mkdir git-practical
cd git-practical
git init
```

**4. Create a file and make your first commit**
```bash
echo "My first version" > notes.txt
git add notes.txt
git commit -m "Add initial notes"
```

**5. Create a feature branch and edit the file**
```bash
git branch feature
 git switch feature
echo "Feature branch change" >> notes.txt
git add notes.txt
git commit -m "Update notes on feature branch"
```

**6. Return to main and make a different edit**
```bash
git switch main
echo "Main branch change" >> notes.txt
git add notes.txt
git commit -m "Update notes on main"
```

**7. Merge the branch**
```bash
git merge feature
```
If Git reports a conflict, open the conflicting file. Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) and keep the content you want. Then:
```bash
git add notes.txt
git commit -m "Resolve merge conflict"
```

**8. View repository information**
```bash
git status
git log --oneline
git branch
```

### Common commands
| Command | Purpose |
|---|---|
| `git clone URL` | Copy a remote repository to your computer |
| `git pull` | Fetch and integrate remote changes |
| `git push` | Upload commits to a remote repository |
| `git add FILE` | Stage file changes |
| `git commit -m "message"` | Save a snapshot in history |
| `git switch BRANCH` | Switch branches |
| `git merge BRANCH` | Merge another branch into the current branch |

### Result
A Git repository was created and used to track changes through commits and branches. Branch merging and conflict resolution were practiced.

---

## 2. Jenkins Installation and Continuous Integration

### Aim
To install Jenkins and create a job that automatically builds a simple Java program.

### Requirements
- Java JDK installed
- Jenkins installed (local installation or Docker)
- Browser

### Steps

**1. Install Java and verify it**
```bash
java -version
```
If Java is missing, install a supported JDK and configure `JAVA_HOME` if your installation requires it.

**2. Install Jenkins**
Download the installer for your operating system from https://www.jenkins.io/download/. Follow the installation wizard and start the Jenkins service.

**3. Open Jenkins**
Visit:
```text
http://localhost:8080
```
Use the initial administrator password displayed by the installer or stored in Jenkins' initial password file. Complete the setup wizard, install suggested plugins, and create an administrator account.

**4. Create a Java program**
Create a folder named `jenkins-java` and add `Hello.java`:
```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello from Jenkins CI!");
    }
}
```

**5. Create a Jenkins job**
- Dashboard → **New Item**.
- Enter `Java-Hello-Job`.
- Select **Freestyle project** → OK.
- In **Build Steps**, select **Execute Windows batch command** on Windows or **Execute shell** on Linux/macOS.

Windows command:
```bat
javac Hello.java
java Hello
```
Linux/macOS command:
```bash
javac Hello.java
java Hello
```
Ensure the job's workspace contains `Hello.java` (for a basic local exercise, you can configure the job to use a workspace containing the file; for repeatable builds, connect source control in the Source Code Management section).

**6. Run the build**
Click **Build Now**. Open the build number → **Console Output**. The output should show compilation and `Hello from Jenkins CI!`.

### Result
Jenkins was installed and configured to execute a build job, demonstrating a basic continuous-integration workflow.

---
## 3. Jenkins with Maven: Automated Build Pipeline

### Aim
To use Maven with Jenkins to compile, test, and package a Java project through a pipeline.

### Requirements
- JDK
- Maven
- Jenkins with Pipeline plugin

### Steps

**1. Verify installations**
```bash
java -version
mvn -version
```

**2. Create a Maven project structure**
```text
maven-demo/
├── pom.xml
└── src/
    └── main/
        └── java/
            └── App.java
```

**3. Add `App.java`**
```java
public class App {
    public static void main(String[] args) {
        System.out.println("Maven project built successfully!");
    }
}
```

**4. Add `pom.xml`**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>maven-demo</artifactId>
    <version>1.0</version>
    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
</project>
```
Use a Java version compatible with your installed JDK.

**5. Run Maven locally** (from the project folder)
```bash
mvn clean
mvn compile
mvn test
mvn package
```
Maven creates output in the `target` directory.

**6. Configure Maven in Jenkins**
- Jenkins Dashboard → Manage Jenkins → Tools.
- Configure the installed JDK and Maven (or allow Jenkins to install configured versions).
- Create a Pipeline job and connect your Git repository if it is hosted remotely.

**7. Add a `Jenkinsfile` to the repository**
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
```
For a Windows Jenkins agent, replace `sh` with `bat`, for example `bat 'mvn clean compile'`. In Jenkins, choose **Pipeline script from SCM** to load the Jenkinsfile from your repository, or choose Pipeline and paste the script. Run the job and review the stage results.

### Result
Jenkins executed Maven stages to compile, test, and package the project automatically.

---
## 4. Docker and Dockerfile

### Aim
To run an application in a Docker container and build a custom image using a Dockerfile.

### Requirements
- Docker Desktop (Windows/macOS) or Docker Engine (Linux)

### Steps

**1. Install and verify Docker**
```bash
docker --version
docker info
```

**2. Download an Nginx image**
```bash
docker pull nginx
```

**3. Run a container**
```bash
docker run -d --name nginx-container -p 8080:80 nginx
```
Open `http://localhost:8080` to see the default Nginx page.

**4. Check container status and logs**
```bash
docker ps
docker logs nginx-container
```

**5. Create a custom web page**
Create a folder `docker-practical`, then create `index.html`:
```html
<!DOCTYPE html>
<html>
<head><title>Docker Practical</title></head>
<body><h1>Hello from my Docker image!</h1></body>
</html>
```

**6. Create a `Dockerfile` in that folder**
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

**7. Build the image** (run inside the folder)
```bash
docker build -t my-web-image .
```

**8. Run your custom image**
```bash
docker run -d --name my-web-container -p 8081:80 my-web-image
```
Open `http://localhost:8081`.

**9. Stop and remove containers**
```bash
docker stop nginx-container my-web-container
docker rm nginx-container my-web-container
docker images
```

### Result
A container was run from a public image and a custom Docker image was built from a Dockerfile.

---
## 5. Docker Compose: Multi-Container Application

### Aim
To use Docker Compose to define and run multiple services together.

### Requirements
- Docker Desktop or Docker Engine with Compose plugin

### Steps

**1. Create a folder**
```bash
mkdir compose-practical
cd compose-practical
```

**2. Create `docker-compose.yml`**
```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: change-this-local-password
      MYSQL_DATABASE: practical_db
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```
This example starts a web server and database together. The web service does not connect to the database; it only demonstrates service orchestration.

**3. Start the services**
```bash
docker compose up -d
```

**4. Check services**
```bash
docker compose ps
docker compose logs
```
Open `http://localhost:8080` to access Nginx.

**5. Stop services**
```bash
docker compose down
```
To also remove the database volume (and its stored data), use:
```bash
docker compose down -v
```

### Result
Docker Compose started and managed multiple services using a single YAML configuration.

---
## 6. Ansible Playbooks

### Aim
To automate simple system configuration tasks using an Ansible playbook.

### Requirements
- Linux, or Ubuntu running through WSL
- Ansible installed

### Steps (Ubuntu/WSL)

**1. Update package information and install Ansible**
```bash
sudo apt update
sudo apt install ansible -y
ansible --version
```

**2. Create a practical folder**
```bash
mkdir ansible-practical
cd ansible-practical
```

**3. Create `inventory.ini`**
```ini
[local]
localhost ansible_connection=local
```

**4. Test connectivity**
```bash
ansible -i inventory.ini local -m ping
```
A successful result should report `pong`.

**5. Create `basic.yml`**
```yaml
- name: Basic local automation
  hosts: local
  gather_facts: false
  tasks:
    - name: Create a directory
      ansible.builtin.file:
        path: /tmp/ansible-practical
        state: directory

    - name: Create a text file
      ansible.builtin.copy:
        content: "Created using Ansible.\n"
        dest: /tmp/ansible-practical/message.txt
```

**6. Run the playbook**
```bash
ansible-playbook -i inventory.ini basic.yml
```

**7. Verify the file**
```bash
cat /tmp/ansible-practical/message.txt
```

### Result
Ansible successfully automated directory and file creation through a playbook.

---
## 7. Kubernetes Deployment

### Aim
To deploy an application on a local Kubernetes cluster and expose it as a service.

### Requirements
- Docker Desktop, or another container runtime
- kubectl
- Minikube

### Steps

**1. Install tools**
Install Docker Desktop, kubectl, and Minikube using their official installation guides.

**2. Start a local cluster**
```bash
minikube start
kubectl cluster-info
kubectl get nodes
```

**3. Create a deployment**
```bash
kubectl create deployment myapp --image=nginx:latest
```

**4. Check deployment and pods**
```bash
kubectl get deployments
kubectl get pods
kubectl describe deployment myapp
```

**5. Expose the deployment**
```bash
kubectl expose deployment myapp --type=NodePort --port=80
kubectl get services
```

**6. Open the application**
```bash
minikube service myapp
```
Minikube should open the service in a browser or print a URL.

**7. Delete resources when finished**
```bash
kubectl delete service myapp
kubectl delete deployment myapp
minikube stop
```

### Result
An Nginx application was deployed in Kubernetes and exposed through a service.

---
## 8. Kubernetes Scaling, Rolling Updates and Rollbacks

### Aim
To scale replicas and perform a rolling image update and rollback in Kubernetes.

### Requirements
- Minikube running
- kubectl

### Steps

**1. Start the cluster**
```bash
minikube start
```

**2. Create a deployment**
```bash
kubectl create deployment webapp --image=nginx:1.25
kubectl get deployments
kubectl get pods
```

**3. Scale to three replicas**
```bash
kubectl scale deployment webapp --replicas=3
kubectl get pods
kubectl get deployment webapp
```

**4. Update the container image**
```bash
kubectl set image deployment/webapp nginx=nginx:1.27
kubectl rollout status deployment/webapp
```

**5. Check rollout history**
```bash
kubectl rollout history deployment/webapp
```

**6. Roll back to the previous version**
```bash
kubectl rollout undo deployment/webapp
kubectl rollout status deployment/webapp
kubectl get pods
```

**7. Inspect and clean up**
```bash
kubectl describe deployment webapp
kubectl delete deployment webapp
minikube stop
```

### Result
The deployment was scaled, updated through a rolling update, and rolled back to a previous revision.

---
## 9. Prometheus and Grafana Monitoring

### Aim
To collect system/application metrics using Prometheus and visualize them in Grafana.

### Requirements
- Prometheus
- Grafana
- An exporter that exposes metrics (for Windows, Windows Exporter; for Linux, Node Exporter)

### Steps (Windows example)

**1. Download Prometheus**
Download and extract Prometheus for Windows from https://prometheus.io/download/. Open PowerShell in its extracted directory.

**2. Configure `prometheus.yml`**
Use a configuration such as:
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: windows
    static_configs:
      - targets: ["localhost:9182"]
```

**3. Start Prometheus**
```powershell
./prometheus.exe --config.file=prometheus.yml
```
Open `http://localhost:9090`. In the query page, enter `up` and execute it to see whether scrape targets are available.

**4. Install an exporter**
Install and run Windows Exporter using its official release/installation instructions. Confirm it exposes metrics on port `9182` (the default may differ by version/configuration). Open `http://localhost:9182/metrics` to verify metrics are present.

**5. Install and start Grafana**
Download Grafana from https://grafana.com/grafana/downloads/ and start it. Open:
```text
http://localhost:3000
```
Sign in and change the default credentials if prompted.

**6. Add Prometheus as a Grafana data source**
- Grafana → Connections → Data sources → Add data source.
- Choose Prometheus.
- Set URL to `http://localhost:9090` if both programs run on the same machine and Grafana can reach it.
- Save and test.

**7. Create a dashboard**
- Create a new dashboard and add a panel.
- Select Prometheus as the data source.
- Use a metric exposed by the exporter (for example, a Windows metric such as `windows_cpu_time_total`, if available in your exporter version).
- Save the dashboard.

### Result
Prometheus scraped metrics from an exporter and Grafana displayed those metrics in a dashboard. Metric names vary with exporter and version.

---
## 10. ELK Stack: Log Monitoring

### Aim
To collect, process, search, and visualize logs using Elasticsearch, Logstash, and Kibana.

### Requirements
- Linux or Ubuntu on WSL
- Elasticsearch, Logstash, and Kibana versions from the same compatible release series
- Adequate RAM; the stack can be resource-intensive

### Steps (Ubuntu/WSL overview)

**1. Update the system**
```bash
sudo apt update
sudo apt upgrade -y
```

**2. Install Elasticsearch, Logstash, and Kibana**
Follow the official Elastic installation instructions for your Linux distribution and install matching versions of all three components. Package repositories and security setup can differ between Elastic releases. Elasticsearch 8.x enables security features by default, so configure the credentials and certificates required by your installed version rather than assuming an unsecured local service.

**3. Start Elasticsearch and verify it**
```bash
sudo systemctl enable --now elasticsearch
sudo systemctl status elasticsearch
```
Use the URL and authentication/certificate settings provided by your installation. For a secured local installation, a curl check may look like:
```bash
curl --cacert /path/to/http_ca.crt -u elasticsearch https://localhost:9200
```
Replace the certificate path and provide the password when prompted; don't put passwords in shared files.

**4. Create a basic Logstash pipeline**
Create a pipeline configuration file (for example, `stdin-demo.conf`) in Logstash's pipeline configuration location:
```conf
input {
  stdin { }
}
filter {
}
output {
  stdout { codec => rubydebug }
}
```
This simple pipeline reads lines from the terminal and prints parsed event data. Start Logstash with the configuration file using the command appropriate for your installation, for example:
```bash
sudo /usr/share/logstash/bin/logstash -f stdin-demo.conf
```
Type a line and observe the output; stop with Ctrl+C. This demonstrates the input-filter-output pipeline. For indexing into Elasticsearch, configure an Elasticsearch output using the credentials and TLS settings required by your installation.

**5. Start Kibana**
```bash
sudo systemctl enable --now kibana
sudo systemctl status kibana
```
Open `http://localhost:5601` and complete the setup/connection steps shown by your version.

**6. Explore logs in Kibana**
Once logs are sent to Elasticsearch:
- Open Kibana → Discover.
- Create/select a data view matching the index pattern (for example, `practical-logs-*`).
- Select the time field if the index contains one.
- Search, filter, and inspect log events.

### Result
The ELK components were explored: Elasticsearch stores and searches events, Logstash processes incoming events, and Kibana provides a UI for exploring and visualizing logs.

---

## Quick Reference

| Practical | Main tool(s) | What you practice |
|---|---|---|
| 1 | Git | Version control, commits, branches, merges |
| 2 | Jenkins | CI job and automated build |
| 3 | Jenkins, Maven | Build, test, package pipeline |
| 4 | Docker | Images, containers, Dockerfile |
| 5 | Docker Compose | Multi-service orchestration |
| 6 | Ansible | Configuration automation and playbooks |
| 7 | Kubernetes | Deployment and service exposure |
| 8 | Kubernetes | Scaling, rolling update, rollback |
| 9 | Prometheus, Grafana | Metrics collection and dashboards |
| 10 | Elasticsearch, Logstash, Kibana | Log processing, search, visualization |


## Notes
- These are learning examples, not production-ready configurations.
- Do not commit passwords, API keys, certificates, private keys, or other secrets to GitHub.
- Use current official installation guides because tool versions and setup requirements change.
- If a command fails, check the installed version, service status, PATH, ports, and permissions.
