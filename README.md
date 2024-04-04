
**Experience Seamless Jenkins Server Deployment for Temporary Projects and Experiments**

Are you seeking a conveniently prepared Jenkins server for your temporary projects or experimental ventures? Look no further.

Allow us to present a sample implementation for swift deployment:

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
 --env-file .env \
 jenkins:v1.0
```


