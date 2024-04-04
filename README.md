**Simplified Setup for Jenkins Server for Short-Term Projects and Experiments**

Need a Jenkins server that you can set up quickly, whether it's for your temporary projects or just to experiment? You're in luck, because we've got just what you need!

Let me introduce you to a straightforward implementation:

## Implementation Guide:

### Configuring the Server on AWS:

Begin by incorporating the following script into the userdata section of your AWS server setup:

#### Initializing the Preconfigured Server with Essential Plugins:

```bash
#!/bin/bash

sudo apt-get update -y && sudo apt-get dist-upgrade -y && sudo apt install unzip -y

#optional 
curl -q "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip -q awscliv2.zip
sudo ./aws/install
/usr/local/bin/aws --version

sudo apt install docker.io -y docker-compose -y
sudo usermod -aG docker ubuntu
newgrp docker

docker pull jenkins/jenkins:lts

cd ~/
git clone https://github.com/rachakondadharmendra/Jenkins-CAC.git
cd Jenkins-CAC/CAC-Jenkins-setup

docker build -t jenkins_cac:v1.0 .	
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

sudo apt-get update -y && sudo apt-get dist-upgrade -y && sudo apt install unzip -y

#optional 
curl -q "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip -q awscliv2.zip
sudo ./aws/install
/usr/local/bin/aws --version

sudo apt install docker.io -y docker-compose -y
sudo usermod -aG docker ubuntu
newgrp docker

docker pull jenkins/jenkins:lts

cd ~/
git clone https://github.com/rachakondadharmendra/Jenkins-CAC.git
cd Jenkins-CAC/Normal-Jenkins-setup

docker build -t jenkins:v1.0 .	
docker run --name jenkins -d -p 8080:8080 -p 5000:5000 \
 -v jenkins_home:/var/jenkins_home \
 -v /var/run/docker.sock:/var/run/docker.sock \
 --privileged \
 --user root \
 --restart always \
 jenkins:v1.0
```


