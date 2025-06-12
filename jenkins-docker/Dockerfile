# Base image
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    wget \
    curl \
    git \
    sudo \
    gnupg2 \
    lsb-release \
    unzip \
    ca-certificates

# Create Jenkins install directory
RUN mkdir -p /opt/jenkins

# Download latest Jenkins WAR
RUN wget -O /opt/jenkins/jenkins.war https://get.jenkins.io/war-stable/latest/jenkins.war

# Expose default Jenkins port
EXPOSE 8080

# Start Jenkins
CMD ["java", "-jar", "/opt/jenkins/jenkins.war"]
