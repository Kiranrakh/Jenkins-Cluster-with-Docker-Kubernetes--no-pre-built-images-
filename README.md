enkins Cluster in Docker & Kubernetes (No Prebuilt Images)
📘 Project Overview
This project demonstrates how to set up a complete Jenkins Master-Agent Cluster manually:

Jenkins Master runs inside a Docker container with Jenkins installed from scratch.

Jenkins Agents run as custom Kubernetes pods, using a manually configured image with OpenJDK and SSH.

The goal was to build the entire cluster without using any prebuilt Jenkins images, gaining full control over every layer of the setup.

🛠️ Technologies & Tools
Docker

Kubernetes (Minikube / kubeadm)

Ubuntu:22.04

Jenkins (installed manually)

SSH (for Master-Agent communication)

🚀 Project Setup Workflow
Phase 1: Jenkins Master Setup in Docker
Create a project directory for the Docker setup.

Write a Dockerfile using the base image ubuntu:22.04.

Manually install OpenJDK, curl, gnupg2, and Jenkins using the official Jenkins Debian repo.

Expose port 8080 and use CMD to start Jenkins.

Build the Docker image and run the Jenkins container.

Access Jenkins on port 8080 and complete the initial setup.

Phase 2: SSH Key Setup for Agent Authentication
Enter the Jenkins master container.

Generate an SSH key pair inside the master (ssh-keygen).

Copy the public key — it will be placed inside the agents' authorized_keys file.

Phase 3: Jenkins Agent Image Build
Create a new project directory for the agent Dockerfile.

Use ubuntu:22.04 as the base.

Install OpenJDK, OpenSSH server, and create a jenkins user.

Add the master's public SSH key to authorized_keys.

Build and tag the image.

Push the image to Docker Hub.

Phase 4: Kubernetes Agent Deployment
Create a Kubernetes Deployment YAML file with agent container details.

Set the container port to 22 for SSH access.

Deploy the agent pods using kubectl apply.

Optionally, expose the deployment using a NodePort service for external SSH access.

Phase 5: Jenkins Master to Agent Connection
In Jenkins UI, create a new node (permanent agent).

Use the Kubernetes Node IP and exposed port.

Configure the connection using SSH with the private key.

Assign a label to the node for use in pipelines.

❌ Final Pipeline Result
Although the Jenkins master and agents were successfully built and deployed, the final pipeline could not connect to the agent pods via SSH due to port and network accessibility issues.

💡 This challenge helped deepen understanding of:

SSH connectivity inside Kubernetes

NodePort access

Container networking

Jenkins node integration and agent lifecycle
🚀 Step-by-Step Setup Guide
✅ Phase 1: Jenkins Master Setup (Docker)
Create a project directory:

mkdir jenkins-docker && cd jenkins-docker

Create a Dockerfile using ubuntu:22.04 and install Jenkins manually:

Install: openjdk-11-jdk, wget, curl, gnupg2, git, and Jenkins repo

Expose port: 8080

Build the image:

docker build -t custom-jenkins .

Run the container:

docker run -d -p 8080:8080 --name jenkins-master custom-jenkins

Get the Jenkins initial password:

docker exec -it jenkins-master cat /var/lib/jenkins/secrets/initialAdminPassword

Access Jenkins UI:

http://<host-ip>:8080

✅ Phase 2: SSH Setup for Agent Authentication
Enter the master container:

docker exec -it jenkins-master bash

Generate SSH key:

ssh-keygen -t rsa

Copy public key:

cat ~/.ssh/id_rsa.pub
Save this for use in agents.

✅ Phase 3: Jenkins Agent Image (Custom Build)
Create a new project directory:

mkdir jenkins-agent && cd jenkins-agent

Create a Dockerfile using ubuntu:22.04:

Install: openjdk-11-jdk, openssh-server, curl

Create jenkins user

Add the master public key to authorized_keys

Build and push agent image:

docker build -t jenkins-agent .

docker tag jenkins-agent <your-dockerhub-username>/jenkins-agent

docker push <your-dockerhub-username>/jenkins-agent

✅ Phase 4: Deploy Jenkins Agents on Kubernetes
Create Kubernetes Deployment YAML:

Set image to your pushed image

Expose container port 22

Set replica count to 2

Apply the deployment:

kubectl apply -f agent-deployment.yaml

Verify the pods:

kubectl get pods

Expose deployment:

kubectl expose deployment jenkins-agent --type=NodePort --port=22

Check service port:

kubectl get svc

✅ Phase 5: Configure Jenkins Master to Use Agent
Go to Jenkins UI → Manage Jenkins → Nodes → New Node

Set Node Details:

Type: Permanent Agent

Host: <Kubernetes node IP>

SSH Port: <NodePort from service>

SSH Username: jenkins

Credentials: Use private key from Jenkins Master

Remote Root Directory: /home/jenkins

Label: k8s-agent

Save and Launch agent

✅ Phase 6: Test CI Pipeline
Create a new Pipeline job