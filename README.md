# DevOps Project - Ashwini

## Banking Application Deployment with Kubernetes, Helm, and Monitoring

This project demonstrates how to build, containerize, deploy, and monitor a simple Banking Java application using a complete DevOps pipeline involving:

- GitHub
- Maven
- Docker
- Kubernetes (Master + Worker nodes)
- Jenkins (CI/CD)
- Helm
- Prometheus & Grafana (Monitoring)

---

## 📁 Repository URL

> https://github.com/ashwiniboddu/Banking.git

---

## ✅ Infrastructure Setup

### 1. Launch EC2 Instances
- **OS:** Amazon Linux 6.1
- **Instance Count:** 3
- **Type:** `t2.medium`
- **Storage:** 20 GB (each)
- **Instance Names:**
  - `jenkins-server`
  - `kubernetes-master`
  - `kubernetes-worker`

---

## ⚙️ Jenkins Server Setup (EC2)

### Connect to Jenkins Server:
```bash
ssh -i <key.pem> ec2-user@<jenkins-ip>
sudo su

Step 1: Install Java, Maven
yum upgrade -y
sudo yum install java-17-amazon-corretto-devel -y
yum install maven -y

Step 2: Install and Configure Docker
yum install docker -y
sudo chmod 666 /var/run/docker.sock
systemctl enable docker
systemctl start docker
systemctl status docker

Step 3: Install Jenkins
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum upgrade
yum install jenkins -y
systemctl daemon-reload
systemctl enable jenkins
systemctl start jenkins
systemctl status jenkins
Step 4: Access Jenkins
URL: http://<jenkins-public-ip>:8080

Retrieve admin password:
cat /var/lib/jenkins/secrets/initialAdminPassword
🔧 Jenkins Configuration
Plugins to Install:
Pipeline:
Stage View
Docker Pipeline
Kubernetes
Prometheus

Configure Maven:
Go to Manage Jenkins -> Global Tool Configuration
Add Maven:
Name: maven-s/w
Enable automatic installation

DockerHub Credentials:
Create personal access token on DockerHub

In Jenkins: Manage Jenkins -> Credentials -> Global -> Add Credentials

Kind: Username and Password
Username: <your-username>
Password: <your-token>
ID: dockerhub-cred

Install kubectl on Jenkins server:
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin/
kubectl version --client
☸️ Kubernetes Cluster Setup
Connect to Both Master and Worker:
ssh -i <key.pem> ec2-user@<instance-ip>
sudo su

Step 1: Install Kubernetes Tools
Follow kubeadm installation steps (or use EKS/Minikube if preferred)

Step 2: Join Worker to Master
On Master:
kubeadm init --pod-network-cidr=192.168.0.0/16
On Worker:
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>

Step 3: Verify Cluster
kubectl get nodes
kubectl get pods -n kube-system
🚀 CI/CD Pipeline with Jenkins


Step 1: Create a New Pipeline Job
Go to Jenkins Dashboard -> New Item -> Pipeline
Name: Banking

Step 2: Configure the Jenkinsfile
Pull code from GitHub
Build app using Maven
Build Docker image and push to DockerHub
Deploy to Kubernetes using kubectl apply

Step 3: Trigger the Pipeline
The pipeline builds and deploys the Banking app.

Verify deployment:
kubectl get pods
kubectl get services
Access the app:

http://<node-ip>:<service-port>
📊 Application Monitoring (Prometheus & Grafana)

Step 1: Install Helm
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
helm version

Step 2: Deploy Prometheus
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus
Access Prometheus:
http://<node-ip>:9090

Step 3: Deploy Grafana
---> helm install grafana prometheus-community/grafana
---> Access Grafana:
http://<node-ip>:3000

Default username: admin
Default password: prom-operator (or check Helm output)

Step 4: Configure Grafana
Add Prometheus Data Source:
Go to Connections -> Data Sources -> Add
Select Prometheus
Set URL: http://<prometheus-service>:9090
Click Save & Test

Import Dashboards:
Node Exporter Full (Node performance)
ID: 1860
Jenkins Performance & Health
ID: 9964

Use: https://grafana.com/grafana/dashboards/

✅ Project Summary
Built a Java Banking app with Maven
Containerized it using Docker
Pushed image to DockerHub
Deployed it into Kubernetes via Jenkins CI/CD
Monitored system and app performance using Prometheus and Grafana
