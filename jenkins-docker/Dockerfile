FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    openjdk-11-jdk \
    wget \
    curl \
    gnupg2 \
    git \
    lsb-release \
    sudo

# Add Jenkins key & repo
RUN curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | gpg --dearmor -o /usr/share/keyrings/jenkins-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian binary/" | tee /etc/apt/sources.list.d/jenkins.list && \
    apt-get update && \
    apt-get install -y jenkins

EXPOSE 8080

CMD ["jenkins"]
