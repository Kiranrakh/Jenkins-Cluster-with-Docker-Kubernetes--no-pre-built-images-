FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    git \
    curl \
    openssh-server \
    sudo && \
    rm -rf /var/lib/apt/lists/*

# Create Jenkins user
RUN useradd -m -s /bin/bash jenkins

# Add jenkins to sudoers (optional but useful)
RUN echo "jenkins ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Setup SSH keys
RUN mkdir -p /home/jenkins/.ssh
COPY authorized_keys /home/jenkins/.ssh/authorized_keys
RUN chown -R jenkins:jenkins /home/jenkins/.ssh && \
    chmod 700 /home/jenkins/.ssh && \
    chmod 600 /home/jenkins/.ssh/authorized_keys

# Fix for sshd error
RUN mkdir -p /run/sshd

# Expose SSH port
EXPOSE 22

# Start SSH server
CMD ["/bin/bash", "-c", "ssh-keygen -A && /usr/sbin/sshd -D"]
