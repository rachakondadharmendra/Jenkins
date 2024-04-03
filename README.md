
**Experience Seamless Jenkins Server Deployment for Temporary Projects and Experiments**

Are you seeking a conveniently prepared Jenkins server for your temporary projects or experimental ventures? Look no further.

Allow us to present a sample implementation for swift deployment:

## Implementation Guide:

### Configuring the Server on AWS:

Begin by incorporating the following script into the userdata section of your AWS server setup:

#### Initializing the Preconfigured Server with Essential Plugins:

```bash
#!/bin/bash

# Update and upgrade system packages
sudo apt-get update -y && sudo apt-get dist-upgrade -y && sudo apt install unzip -y

# Install Docker and Docker Compose
sudo apt install docker.io -y docker-compose -y

# Add current user to the docker group
sudo usermod -aG docker ubuntu
newgrp docker

# Pull the latest Jenkins Long Term Support (LTS) image from Docker Hub
docker pull jenkins/jenkins:lts

# Clone the necessary Git repository
git clone <git repository URL>
cd CAC-Jenkins-setup

# Build the Docker image tagged as 'jenkins_cac:v1.0'
docker build -t jenkins_cac:v1.0 .

# Run the Docker container named 'jenkins' with specified configurations
docker run --name jenkins -d -p 8080:8080 -p 5000:5000 \
 -v jenkins_home:/var/jenkins_home \
 -v /var/run/docker.sock:/var/run/docker.sock \
 --privileged \
 --user root \
 --restart always \
 --env-file .env \
 jenkins_cac:v1.0
```

#### Setting up Jenkins Server in a Standard Configuration:

```bash
#!/bin/bash

# Update and upgrade system packages
sudo apt-get update -y && sudo apt-get dist-upgrade -y && sudo apt install unzip -y

# Install Docker and Docker Compose
sudo apt install docker.io -y docker-compose -y

# Add current user to the docker group
sudo usermod -aG docker ubuntu
newgrp docker

# Pull the latest Jenkins Long Term Support (LTS) image from Docker Hub
docker pull jenkins/jenkins:lts

# Clone the necessary Git repository
git clone <git repository URL>
cd Normal-Jenkins-setup

# Build the Docker image tagged as 'jenkins:v1.0'
docker build -t jenkins:v1.0 .

# Run the Docker container named 'jenkins' with specified configurations
docker run --name jenkins -d -p 8080:8080 -p 5000:5000 \
 -v jenkins_home:/var/jenkins_home \
 -v /var/run/docker.sock:/var/run/docker.sock \
 --privileged \
 --user root \
 --restart always \
 jenkins:v1.0
```


