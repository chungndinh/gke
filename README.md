Build base by nginx and php-fpm on alpine linux
Demo Docker-Github-Jenkins-GKE
####Build Jenkins Docker
1. Install Docker
2. Run Docker Jenkins:
docker run -u 0 --privileged --name jenkins_2 -it -d -p 8181:8080 -p 50000:50000 \
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which docker):/usr/bin/docker \
-v /home/jenkins_home:/var/jenkins_home \
jenkins/jenkins:latest
3. Install kubectl in Jenkins Docker:
docker exec -it jenkins_2 /bin/bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
vim /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
apt-get update
apt-get install kubectl -y

####Create GKE
1. Create GKE (noted: add Cloud NAT to pull images from dockerhub and enable API )
2. Add key JSON
IAM & ADMIN -> Service Account-> Create Service Account ->Member ID
IAM-> Add -> add member ID and Role: kubernetes engine admin
-->create key

####Config Jenkins
1. Install Plugin
Docker Pipeline
Google kubernetes Engine
Pull Request Builder
2. Add Credential 
Credential->System-Global credential->Add Credential
- Key for DockerHub Account:
    Kind: secret text
    Secret: password docker
    ID&Description: dockerhub
- Key for Google Service Account from private key
    Kind:Google Service Account from private key
    Project nane: kubernetes
    Check JSON key and upload key from GCP
3. Edit file Jenkinsfile in Github
- Enviroment (Project iD, Clustername,location, Credenential-ID='Project Name' in Key)
- sh "docker login -u <user docker> ....

4. Add webhook in Github-> <URL Jenkins>/github-webhook

5. Create Item in Jenkins pipeline
- Pipeline->Git->Git Repository URL
- Check tick GitHub hook trigger for GITScm polling



