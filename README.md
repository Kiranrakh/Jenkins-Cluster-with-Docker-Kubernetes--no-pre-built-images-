


```markdown
# 🚀 Jenkins Cluster using Docker & Kubernetes (No Prebuilt Images)

## 📘 Project Overview

This project demonstrates how to manually build and configure a **Jenkins Master-Agent CI/CD Cluster** from scratch — **without using any prebuilt images**.  
The Jenkins Master runs inside a Docker container, while Jenkins Agents are deployed as **custom-built Kubernetes Pods** using SSH-based communication.

> 🎯 Goal: Learn how Jenkins really works under the hood — install everything manually, configure secure communication, and build a real-world pipeline-ready setup.

---

## 🛠️ Technologies & Tools Used

- Docker 🐳  
- Kubernetes ☸️ (Minikube / kubeadm)  
- Ubuntu 22.04 🐧  
- Jenkins (installed manually) ⚙️  
- SSH 🔐

---

## ⚙️ Project Setup Workflow

### ✅ Phase 1: Jenkins Master Setup (Docker)

1. Create a project folder:  
   `mkdir jenkins-docker && cd jenkins-docker`

2. Build a Dockerfile (base: `ubuntu:22.04`) that manually installs:
   - `openjdk-11-jdk`, `wget`, `curl`, `gnupg2`, `git`, and Jenkins repo
   - Expose port `8080`

3. Build and run Jenkins container:
```

docker build -t custom-jenkins .
docker run -d -p 8080:8080 --name jenkins-master custom-jenkins

```

4. Get Jenkins admin password:
```

docker exec -it jenkins-master cat /var/lib/jenkins/secrets/initialAdminPassword

```

5. Access Jenkins:  
`http://<host-ip>:8080`

---

### ✅ Phase 2: SSH Key Setup for Agent Authentication

1. Enter the Jenkins master container:  
`docker exec -it jenkins-master bash`

2. Generate SSH key:  
`ssh-keygen -t rsa`

3. Copy public key:  
`cat ~/.ssh/id_rsa.pub`  
*(This will be used in the agents’ `authorized_keys`)*

---

### ✅ Phase 3: Jenkins Agent Image (Custom Build)

1. Create a new folder:  
`mkdir jenkins-agent && cd jenkins-agent`

2. Build a custom Dockerfile with:
- Base image: `ubuntu:22.04`
- Install: `openjdk-11-jdk`, `openssh-server`, `curl`
- Create `jenkins` user and add master's public key to `authorized_keys`

3. Build and push to Docker Hub:
```

docker build -t jenkins-agent .
docker tag jenkins-agent <your-dockerhub-username>/jenkins-agent
docker push <your-dockerhub-username>/jenkins-agent

```

---

### ✅ Phase 4: Deploy Jenkins Agents on Kubernetes

1. Create a `Deployment YAML` that:
- Uses the pushed image
- Exposes container port `22`
- Sets replicas to `2`

2. Apply the deployment:
```

kubectl apply -f agent-deployment.yaml
kubectl get pods

```

3. Expose the deployment:
```

kubectl expose deployment jenkins-agent --type=NodePort --port=22
kubectl get svc

````

---

### ✅ Phase 5: Connect Jenkins Master to Kubernetes Agent

1. Jenkins UI → **Manage Jenkins** → **Nodes** → **New Node**

2. Configuration:
- Type: `Permanent Agent`
- Host: `<Kubernetes node IP>`
- Port: `<NodePort from service>`
- Username: `jenkins`
- Credentials: Add SSH private key from Master
- Remote Directory: `/home/jenkins`
- Labels: `k8s-agent`

3. Save & launch the agent connection.

---

### ✅ Phase 6: Test CI Pipeline

1. Create a new **Pipeline Job**.

2. Use a script like:
```groovy
pipeline {
  agent { label 'k8s-agent' }
  stages {
    stage('Build') {
      steps {
        sh 'echo Hello from Kubernetes Agent'
      }
    }
  }
}
````

3. Run the pipeline and check if it connects to the agent.

---

## ❌ Final Pipeline Result

Although Jenkins Master and Agents were successfully built and deployed, the final CI pipeline **failed to connect via SSH** to the agent pods due to NodePort/networking issues.

---

## 💡 What I Learned

* SSH connectivity in Kubernetes
* Custom Docker image creation
* Jenkins agent configuration and troubleshooting
* Pod-to-host network communication
* Secure CI/CD architecture from scratch

---

## 🙏 Acknowledgments

A huge thanks to **Vimal Daga Sir** and **LinuxWorld Informatics Pvt. Ltd.** for motivating us to learn the DevOps way — by solving real-world problems, not just following instructions.

---

## 📌 What’s Next?

* Fix SSH access from Jenkins master to K8s agents
* Run a successful Jenkins pipeline on Kubernetes pods
* Add GitHub webhook integration & automated builds
* Add notifications via AWS SNS or Slack

---

> 🧠 This project wasn’t just about success — it was about understanding, breaking, fixing, and **learning deeply by doing.**

```

---
