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

# Configure Github Webhook>
Configuring Github Webhook will automatically trigger the Pipeline if there is nay changes made on the code.

- On the Github Project repository, click on settings
- Scroll down, at the left corner, look for and select webhooks
- click on add webhooks, confirm your password
- Under PayLoad URL, copy jenkins URL (http://<server-IP>:8080/github-webhook/)
- Scroll down under 'Which events would you like to trigger this webhook?"
      click on select individual events, then tick, pull request and pushes events
- Click on add webhooks

![webhook](https://user-images.githubusercontent.com/101070055/234562655-179506bd-d812-40ab-8fd3-ec0c10707195.png)
       
- Build the PipeLine
          
![success-build](https://user-images.githubusercontent.com/101070055/234563225-3fc117f1-5aee-4003-a3d5-87896e9c2bc2.png)
          
- test the webhook, by making any changes on code repository, if the changes is commited. it will automatically trigger pipeline to poll the repository and rebuild again.
          
# Configure Sonarqube   

          sudo apt install unzip          
          sudo  apt update -y
          sudo apt install openjdk-17-jre -y
          wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
          unzip sonarqube-9.9.0.65466.zip          

- Start Sonarqube
          
          cd sonarqube-9.9.0.65466 && cd bin && cd linux-x86-64 
          ./sonar.sh start
- To Access Sonarqube, open port 9000 on inbound rule attached to the sonarqube server.
          http://<Server-Ip>:9000
          
-  Enter default password, and after change the password      
          
- Sonarqube dashboard
          
![sonar-dash](https://user-images.githubusercontent.com/101070055/234571944-6366debe-04e5-4c66-b257-f4b31114951e.png)

- Setup a project, scroll down click on manually
          
![sonar-project](https://user-images.githubusercontent.com/101070055/234572691-05512182-e7d4-4802-871e-c1a80a1c03d8.png)

- Enter information and project name, click setup button
          
- Select Jenkins to configure analysis 
          
          Select Github
          Click configure analysis, and click continue to step 3
          Select other ( my project is just html, if is soring-boot, maven will be selected)
- Copy project key and click finish tutorial
          
 ![project-key](https://user-images.githubusercontent.com/101070055/234575691-d6fde9d5-366e-41f3-84df-9a7578247107.png)
          
-Create a token
          
         Go to admin account, click on security
         Enter token name, select global analysis token type  and expiry date. 
         Click generate
       
