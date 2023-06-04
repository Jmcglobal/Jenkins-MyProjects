# JENKINS CI AND ARGOCD

![linkedn-content-1](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/eab6f7f9-9d98-4811-a840-51f0b107a814)

End-to-end Jenkins pipeline for a Java application using Git, SonarQube, Docker, Argocd and Kubernetes

## Prerequisites:

                - java Application hosted on git repository
                - Separate Git repository hosted for Argocd
                - Jenkins and Sonarqube server
                - Kubernetes cluster
                - Argocd Setup inside the kubernetes cluster

#### I will be using AWS EC2 ubuntu for my jenkins server and sonarqube server (t3.medium)

- How to install jenkins on ubuntu linux machine

https://github.com/Jmcglobal/Jenkins-MyProjects/blob/master/Jenkins-CICD-00.md

#### For the kubernetes cluster, i will be using minikube cluster either on my local machine or virtual machine

- How to Install minikube cluster

https://github.com/Jmcglobal/Kubernetes-MyProject/tree/master/local-minikub

- How to create KOPs cluster

https://github.com/Jmcglobal/Kubernetes-MyProject/tree/master/KOPS
  
- How to create EKS cluster

https://github.com/Jmcglobal/Kubernetes-MyProject/tree/master/EKS

#### jenkins - Sonarqube server 

![jenkins-server](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/4675f921-5e51-4a1a-80e9-644ca2071d93)

#### Argocd Setup on kubernetes cluster

- How to setup argocd on kubernetes cluster

https://github.com/Jmcglobal/DevOPs-Projects-01/blob/master/My-Project/GitOps-ArgoCD.md

#### Description

- project repository

https://github.com/Jmcglobal/java-app

- Argocd manifest repository

https://github.com/Jmcglobal/argocd-java-app

Two repository is setup  on git, one for application codes, and one for gitops argocd continous delivery

I have also configured argocd manifest repository with empty deployment yaml file and service yaml file, on argocd controller server, i enable auto synchronization as well. Therefore it is going to synchronize empty manifest file, so that once the pipeline is successfull, it will push the declarative manifest file with a current build tag, then argocd controller will automatically synchronize it.

#### Argocd is ready and auto sync enabled

![argocd-empty-yml](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/fea1e834-5b69-4177-aadc-6fdf97f0eaf4)

If you wish to test this project, setup another git repository on your git account, clone the project application repo. 

I have written all the script on JenkinsFile, follow the guildeline above and below to deployed it

- Whenever a development team uplaods a code to github project repo, it will trigger a jenkins  pipeline to do the following:

        - Run a unit testing with maven, building and compiling and testing the codes, if successfull and no error,
        - Run code quality analysis on sonarqube server,
        - If code analysis check is passed,
        - Copy the application  artifact and build the application image using docker
        - After building the image, It will push it to dockerhub image repository, with a build number as tag
        - use stream editor and add current tag to the deployment yaml file
        - check for argocd repo directory and, if not exist create "argocd-java-app"
        - Check for java-manifest dir inside argocd-java-app, if not exist, create one
        - check for manifest file inside java-manifest, if not exist copy deployment and service yaml file from java-app-yaml
        - Do git init, git add and commit
        - push the yaml files to argocd repository

- Once the yaml files is pushed to argocd repository

 argocd controller server, that monitors the repository will automatically sync the chnages and deployed the application deployment and service yaml files to the kubernetes cluster. 
(dev cluster can be done automatically, while production kubernetes cluster  can be sync manually, if there if the application have been tested on dev cluster and no bugs on the app )

### Configure jenkins

- My AWS ubuntu EC2 instance is ready, and ssh inside the instance to download necessary softwares and dependencies

- Install Java

 sudo apt update -y
 sudo apt install openjdk-11-jre
 
- verify java is installed

java --version

![java-version](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/d35554f5-8399-4742-b336-432f950ee502)

- Install Jenkins

        curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
                 /usr/share/keyrings/jenkins-keyring.asc > /dev/null
               echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
                 https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
                 /etc/apt/sources.list.d/jenkins.list > /dev/null

    sudo apt-get update
    sudo apt-get install jenkins 
    
- Once jenkins is installed, it will start automatically on port 8080, therefore to reach jenkins UI, i have allow port 8080 on EC2 security group, to  be reachable from anywhere. 

- To access jenkins UI

      http://<EC2-instance-public-IP>:8080
      
![jenkins-default-home-page](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/4da46fe0-9a61-4e51-9c76-4fe7d0108945)

- To get started with jenkins, you need default password which is located at "/var/lib/jenkins/secrets/initialAdminPassword "

     sudo  cat /var/lib/jenkins/secrets/initialAdminPassword
     
- Copy the password, and paste it, then click continue. select install suggested plugins 

![select-install-sugges-plugins](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/36a38180-392e-45b8-b09c-f4ceae72aeb3)

- Once the plugins is installed, create first admin user, by entering required details, such as username, password, name and email. then save and continue, the save and finish

![create-admin-user](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/1d7102b5-f2c2-4ff2-89e4-1fe664c97fee)

#### Jenkins default homepage

![jenkins-default-home](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/e9456b9e-20da-4a47-bce3-4bc885a83024)

### Create a piepline

- On jenkins dashboard, click on new item, enter name and select pipeline, then click okay

![jenkins-argocd-pipeline](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/cc403810-7146-42c2-8a10-5729441c48e0)

- Scroll down, to pipeline. This is where you can write pipeline script, in stages

- I have write a jenkinsfile which is available on source code management (Git).

#### Why jenkinsfile

It is reusable at any time and can be shared

Therefore, select pipeline script from SCM

- i also make use of docker as an agent, which is an image that containes maven and docker, so that all the script will run on the docker container, instead of running directly on the jenkins server. After running the pipeline successfully, it will remove the container, therefore there won't be any heavy load on the server. I have wrote the dockerfile and build the image, and it is available on dockerhub for many use cases.

#### Install plugins

- On jenkins dashboard, click on manage jenkins, click on plugins, and select available plugins, the search and install the following plugins

    Docker pipeline plugin
    SonarQube Scanner plugin

#### Install Sonar server

- Configure seperate user for sonarqube

    sudo su -
    adduser sonarqube
    Enter password, Confirm password, press enter for other parameters till done
    apt install unzip -y

    su - sonarqube   >> Switch to sonarqube user
    
- Install Sonar server on EC2 Instance

    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
    unzip *
    
- Grant permission and chnage sonarqube ownership

    chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
    chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424

- Start Sonar Server

    cd sonarqube-9.4.0.54424
    cd bin/linux-x86
    ./sonar.sh start
    
- Access the sonarqube server with 

    http://<server-IP>:9000

Enter default password > change password  >> then access sonar home page
  
  admin as passowrd and admin as user
    




