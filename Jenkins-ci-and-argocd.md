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

- Argocd repository
- 
https://github.com/Jmcglobal/argocd-java-app

Two repository is setup  git, one for application codes, and one for gitops argocd continous delivery

If you wish to test this project, setup another git repository on your git account, clone the project application repo. 

I have written all the script on JenkinsFile, follow the guildeline above and below to deployed it

- Whenever a development team uplaods a code to github project repo, it will trigger a jenkins  pipeline to do the following:

        - Run a unit testing with maven, building and compiling and testing the codes, if suuccessfull and no error,
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



