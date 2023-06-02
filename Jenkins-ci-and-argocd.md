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
