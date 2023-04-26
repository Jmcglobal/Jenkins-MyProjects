# Jenkins CICD AUTOMATION.

project sample code available on github https://github.com/Jmcglobal/Html-site.git

![CICD-1](https://user-images.githubusercontent.com/101070055/234550020-65646baa-325e-4f1b-b2d7-b37fcaaaee61.png)

# Prerequisite

# Jenkins.
Jenkins is used to automate the different stages of Software development and deployment and can be used to manage projects of any size.

# Github.
Github is a code repository that provides version control and collaboration tools, 

# Sonarqube 
Sonarqube is a code quality management tool that helps analyze and manage code quality in software projects.

# Docker.
Docker is a containerization platform that helps simplify the deployment of software applications.

- First i will create an 3 AWS EC2 instance for Jenkins Server. Sonarqube Server, and Docker Server.
- I will set up a Github repository and integrate it with Jenkins. Configure Sonarqube to analyse code quality , Finally create a Docker image of the application and deploy it using jenkins.

![Server-sample](https://user-images.githubusercontent.com/101070055/234552367-b9bf8cf8-8099-4e51-96af-01edb4c16c50.png)

# Step-1.
- Configure Jenkins Server

- Install java runtime environment and jenkins

          sudo apt update -y
          sudo apt install openjdk-11-jre -y

           curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
            /usr/share/keyrings/jenkins-keyring.asc > /dev/null
             echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
            https://pkg.jenkins.io/debian binary/ | sudo tee \
            /etc/apt/sources.list.d/jenkins.list > /dev/null

            sudo apt update -y
            sudo apt install jenkins -y
    
 - Open port 8080 on the security group attached to the Ec2 instances.

            Access jenkins on http://<Server-Public-IP>:8080
            
 - Follow the guide to get defauolt password, then select install suggested plugins     
 - Create First Admin user access, by entering username, password , name and email.

 - Jenkins dashboard

![jenk-dashboard](https://user-images.githubusercontent.com/101070055/234557599-74fe2999-b162-456b-9314-c63e5e6f8e2a.png)


# Step2 Create PipeLine

- Click on new item, Enter pipeLine name, and select freestle project and scroll down then okay
- Inside PipeLine configuration, select SCM (source code management) and click Git, configure the remote repository

![Git-configure](https://user-images.githubusercontent.com/101070055/234559991-601ec4c0-7939-4917-8fc8-61940ca35d57.png)

- Select Build Triggers, click on Github Hook trigger for GITScn polling; this will enable the pipeline to trigger automatically, if there is any changes on remote repository. then apply and save.

![build-trigger](https://user-images.githubusercontent.com/101070055/234560022-1edc39ae-d8bd-4524-b072-19b47e7ace32.png)






