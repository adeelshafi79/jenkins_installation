# Jenkins Installation Script and Guide

This repository contains a Bash script (`install_jenkins.sh`) to automate the installation and setup of Jenkins on a Linux system. The script performs several steps to ensure Jenkins is installed, configured, and running as a systemd service.

## Bash Script (`install_jenkins.sh`)

```bash
#!/bin/bash

# Check if Java is installed, if not, install it
if ! command -v java &> /dev/null; then
    echo "Java not found. Installing Java..."
    sudo apt update
    sudo apt install -y default-jre
fi

# Download Jenkins WAR file
echo "Downloading the .WAR file of Jenkins"
if wget -q https://get.jenkins.io/war-stable/latest/jenkins.war; then
    echo "Download successful"
else
    echo "Download failed"
    exit 1
fi

# Set up Jenkins
echo "Setting up Jenkins"
sudo mkdir -p /opt/jenkins
sudo mv jenkins.war /opt/jenkins/
sudo useradd --system jenkins

# Create Jenkins service file
echo "Creating the Jenkins service"
sudo tee /etc/systemd/system/jenkins.service > /dev/null <<EOF
[Unit]
Description=Jenkins Service
After=network.target

[Service]
User=jenkins
Environment="JENKINS_HOME=/opt/jenkins/.jenkins"
ExecStart=/usr/bin/java -jar /opt/jenkins/jenkins.war --httpPort=8080
Restart=always
RestartSec=10
SyslogIdentifier=jenkins

[Install]
WantedBy=multi-user.target
EOF

# Start Jenkins
echo "Starting Jenkins service"
sudo systemctl daemon-reload
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Check Jenkins service status
echo "Jenkins service status:"
sudo systemctl status jenkins
```
## Steps Covered by the Script

1. **Check for Java Installation:**
   - If Java is not found (`! command -v java`), it installs the default Java Runtime Environment (`default-jre`).

2. **Download Jenkins WAR File:**
   - Downloads the latest stable Jenkins WAR file from [https://get.jenkins.io/war-stable/latest/jenkins.war](https://get.jenkins.io/war-stable/latest/jenkins.war).

3. **Set up Jenkins:**
   - Creates the directory `/opt/jenkins` and moves the downloaded `jenkins.war` file into it.
   - Adds a system user `jenkins` for running Jenkins.

4. **Create Jenkins Service:**
   - Defines a systemd service unit file (`jenkins.service`) to manage the Jenkins service.
   - Configures the service to use `/opt/jenkins/.jenkins` as Jenkins home directory.
   - Specifies Java execution command to start Jenkins on port 8080.

5. **Start Jenkins Service:**
   - Reloads systemd to apply changes.
   - Starts the Jenkins service and enables it to start on system boot.

6. **Check Jenkins Service Status:**
   - Verifies the status of the Jenkins service to ensure it started successfully.

## Usage

1. **Clone the Repository:**
   ```bash
   git clone <repository-url>
   cd jenkins-installation-script

