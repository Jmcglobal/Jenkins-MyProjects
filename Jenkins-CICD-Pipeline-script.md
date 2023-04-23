## Jenkins CICD PipeLine Script

Jenkins CICD Pipeline script is a script written in Groovy that defines a set of instructions and stages to be executed in order to set up a Continuous Integration/Continuous Delivery (CICD) pipeline. 

A CICD pipeline is an automated process that allows developers to build, test and deploy software applications quickly and reliably. The Jenkins CICD Pipeline script is typically used to define the stages of the pipeline, including code checkout, build, test and deploy. 

It can also be used to define parameters that can be used to control the execution of the pipeline, such as environment variables and options.

# Project Overview

![cicd-manual](https://user-images.githubusercontent.com/101070055/233812086-0308dad4-40d8-4944-9277-1525e1132b21.png)

- Above is the map details of this simple CICD project demostration..
- I will be using AWS EC2 Ubuntu Instance as my jenkins Server, and another instance for Sonarqube server.

- Jenkins Server prerequisite

        Java Installation
        maven
        docker
        Git
        jenkins
        
- Installations
 
         sudo apt update -y && sudo apt upgrade -y
         sudo apt install openjdk-17-jdk -y
         sudo apt install docker.io -y
         sudo apt install maven -y
         sudo apt install git -y
 
- jenkins installation

 Debian package repository of Jenkins to automate installation and upgrade
 
- To use this repository, first add the key to your system.

          curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
            /usr/share/keyrings/jenkins-keyring.asc > /dev/null

- Then add a Jenkins apt repository entry

        echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
          https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
          /etc/apt/sources.list.d/jenkins.list > /dev/null

- Run update and Installation Command

        sudo apt update -y
        sudo apt install jenkins -y

- To Access jenkins GUI

               http://<Server-IP>:8080
              
 Follow the password guid to get default password, install suggested plugins, enter details and access jenkins dashboard.
 
 ![jenkins-dash](https://user-images.githubusercontent.com/101070055/233812661-f0c2bcf0-1f7d-451b-b987-7ade0e838c10.png)
