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

![scm-pipeline](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/b9a23ef6-7ec3-4998-a9bb-55c4568ff02f)

![argocd-scm-pipeline](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/775007aa-83f2-4584-9f49-70cd4b0db759)

- Apply and save

- i make use of docker agent, which is an image that containes maven and docker environment and dependecies to run the script, so that all the script will run on the docker container, instead of running directly on the jenkins server. After running the pipeline successfully, it will remove the container, therefore there won't be any heavy load on the server. I have wrote the dockerfile and build the image, and it is available on dockerhub for many use cases.

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
          
![sonarqube-homepage](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/2224fedc-7234-4e11-a17c-c2dd6ed13984)
    
- Configure Token, to enable jenkins authentication to run code quality check

        Go to admin account, click on security then create token
        Copy the token, then configure sonar credentials on jenkins

![jenkins-sonar-token](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/3dc50bec-b446-4a95-b70c-c40846487c58)

#### ADD SONAR CREDENTIALS ON JENKINS

       On jenkins home page, click on manage jenkins >> credentails >> system >> global credentails >> click add credential
       Under kind select secret text
       paste the token
       enter ID name and save

![configure-crede](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/23b61287-9e94-44bb-b8c5-0f47fac3c715)


#### Install Docker and add permisions for jenkins and ubuntu user

-  cange directory to root , exit from sonarqube user terminal

      apt install docker.io -y
      usermod -aG docker jenkins
      usermod -aG docker ubuntu
      newgrp docker
      systemctl restart docker

-  Adding both the jenkins and ubuntu to docker group, will enable them to execute docker command without asking for sudo permission

#### Add docker credentials

       On jenkins home page, click on manage jenkins >> credentails >> system >> global credentails >> click add credential
       under kind, select username and password
       enter your dockerhub username
       enter password
       and enter ID name

![docker-credential](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/2e2a3c45-10cd-436c-bac1-c2a72da875ae)

- Add GitHub credentials

       click add credentials,
       under kind select  secret text
       enter your github token as your secret
       and enter ID name
       
- To get github access token, click click settings from your github account
- scroll down to developer settings, click on it
- Click on personal access token, and select token classic
- then click generate new token

![github-cred](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/87e0189f-d6ab-4408-bce4-bb9c4edebae2)

![all-credentials](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/55dbb03c-b67f-41d7-b9bc-38a6f55d8278)

I simplified this process without adding complex configuration on jenkins, by using docker agent and JenkinsFile.

- On the jenkins file, i also make use of environment variables to simplified the pipeline script

### Stages on the JenkinsFile

      pipeline {
        agent {
          docker {
            image 'jmcglobal/docker-agent-maven:v1'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
          }
        }

        stages {
          stage('Checkout') {
            steps {
              sh 'echo passed'
              //git branch: 'master', url: 'https://github.com/Jmcglobal/java-app.git'
            }
          }

          stage('Build and Test') {
            steps {
              // build the project and create a JAR file
              sh 'cd spring-boot-java && mvn clean package'
            }
          }

    
          stage('Static Code Analysis') {
            environment {
              SONAR_URL = "http://3.231.207.115:9000"  // sonar-server which is running on same server jenkins server
            }
            steps {
              withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
                sh 'cd spring-boot-java && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
              }
            }
          }    
    
    
          stage('Build and Push Docker Image') {
            environment {
              DOCKER_IMAGE = "jmcglobal/java-app:${BUILD_NUMBER}"
              // DOCKERFILE_LOCATION = "spring-boot-java/Dockerfile"
              REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
              script {
                  sh 'cd spring-boot-java && docker build -t ${DOCKER_IMAGE} .'
                  def dockerImage = docker.image("${DOCKER_IMAGE}")
                  docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                      dockerImage.push()
                  }
              }
            }
          }    
    

- Below is the script i used to edit deployment yaml file, to latest build tag, and push it to argocd github repository automatically. The idea is that, After the building and pushing the application image to dockerhub, it will automatically update the deployment yaml file to latest version of the build tag, and push it to argocd repository, then it will be automatically deployed to kubernetes clusters, without manual process. And there is webhook trigger, that will trigger the jenkins pipeline to run automatically, whenever the development teams commit changes on the project repository

                stage('Update Deployment File') {
                    environment {
                        GIT_REPO_NAME = "argocd-java-app"
                        GIT_USER_NAME = "Jmcglobal"
                    }
                    steps {
                        withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                            sh '''                          
                                BUILD_NUMBER=${BUILD_NUMBER}
                                sed -i '19 s/.*/        image: jmcglobal\\/java-app:'$BUILD_NUMBER'/' spring-boot-yaml/deployment.yml
                                if [ ! -d "argocd-java-app/java-manifest" ]; then mkdir -p argocd-java-app/java-manifest; fi
                                cd argocd-java-app 
                                git config --global --add safe.directory /var/lib/jenkins/workspace/jenkins-argocd/argocd-java-app
                                rm -rf .git && git init
                                git remote add origin https://github.com/Jmcglobal/argocd-java-app.git
                                rm -rf java-manifest
                                git config pull.rebase true && git pull origin master
                                cd java-manifest && rm -rf deployment.yml service.yml
                                if [ ! -f "deployment.yaml" ] && [ ! -f "service.yaml" ]; then cp -r ../../spring-boot-yaml/* .; fi
                                cd .. 
                                git add .
                                git config user.email "mmadubugwuchibuife@gmail.com"
                                git config user.name "Mmadubugwu Chibuife"
                                git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                                git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:master
                                rm -rf .git
                                cd java-manifest && rm -rf deployment.yml service.yml                   
                            '''
                        }
                    }
                }
              }
            }

- Deveopers can focused on coding and committing their work on central project repo, jenkins pipeline will be triggered, and run the testing and all required stages, then then argocd will deployed the software to kubernetes cluster, and anyone in the team can test the application progress and feature. With the script above, after all the required stages and testing have completed, it will pull and merge, update version tag, commit and also push it back to argocd repo server. There wont be any error 

   ### RUN THE PIPELINE 
   
Before running the jenkins ci script here is the current state of the argocd server and kubernetes cluster 

![argocd-state](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/44d3f233-f10d-4020-97cd-4c64f62bc534)

![cluster-state](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/a3593ce9-62cf-4cc2-a073-948dd18a7874)
   
### Build now

![buikd-pipeline](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/81489cf7-7e34-4df6-b1f2-38fab35554f3)

Click on build now
    
#### pipeline successfully completed

![building-argocd](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/996be238-70ad-4df5-90f5-b59b7f1a96f1)

#### Quality code checked passed    
    
![quality-check-passed](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/68a30df2-002e-4e2d-b875-fe7a4eeae79b)

#### Automatically pushed image to dockerhub

![images-pushed-to-docker](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/3c8f17bb-3556-42b1-8e52-e2a486f66211)

#### Automatically synchronized and applied to kubernetes clusters

![automatically-sync-repo](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/e6609882-0e93-4712-8a0c-85e928a94562)

#### Application running on kubernetes cluster and reachable from outside world

![kubernetes-synchronized](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/e5d691ed-b4ea-470d-b5c1-92a24316d95a)

        http://<node-ip>:30446

![application-view](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/9f306955-1de2-41f9-bdfb-4232ff4daa7a)

- I can deployed ingress controller, and configure application ingress rule.

- Deploy ELK Stack, Grafana, and prometheus for log analysis, alert and resource visualisation 

- In argocd, i can as well add external clusters, for example, i can add many development clusters and production clusters to argocd, and enable automatic synchronization on development cluster, while production cluster can be manual sync, after client have confirmed all features of the application is functional.    

Email trigger will send an email notification to devops team, if the pipeline script is successfull or failed, with a build tag number.

- How to configure email trigger

https://github.com/Jmcglobal/Jenkins-MyProjects/blob/master/Jenkins-CICD-Pipeline-script.md
       
##### Ultimate Overview of the project

![ultimate-jenkins-argocd](https://github.com/Jmcglobal/Jenkins-MyProjects/assets/101070055/be1bc9fe-5a1e-42a9-8cbf-654c9a1e3f64)

- If there is any question, do >>>>>>>>>>>>>>>>>>
- Reach out to: mmadubugwuchibuife@gmail.com

       
       
