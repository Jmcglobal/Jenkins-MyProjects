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
            
 - Follow the guide to get default password, then select install suggested plugins     
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
       
# Integrate Sonarqube in Jenkins
          
- Click on manage jenkins under jenkins dashboard 
          
          select manage plugin
          click available plugins
          Search for SonarQube Scanner
          click on install without restart

![sonar-scanner](https://user-images.githubusercontent.com/101070055/234578940-77bf205d-d713-4cab-a4ae-5dde1274fd5d.png)
          
          Also Search for SSH2 Eazy plugin, and install
          
- SSH2 Eaazy Plugin, will enable jenkins to execute  linux command to remote server     

- Click on manage Jenkins, click global tool configuaration  
          
          Scroll down to scanner
          click on add, name it any name, select install automatically, apply and save.
          
![sonar-install](https://user-images.githubusercontent.com/101070055/235926276-1d4280c3-5a4c-402d-a43c-b3f7e253bd47.png)

- Under Mange jenkins, click Systems
          
          Scroll down to sonarqube Servers installation,
          Click add sonarqube

![sonar-server](https://user-images.githubusercontent.com/101070055/234582040-b1f247aa-af79-4cc1-a507-4e0cf26a0099.png)

- Save
          
- Add Credentials for sonar scanner
          
          Under server authentication token, click on add
          select secret text under kind
          paste the token copied from sonarqube under secret
          Enter any name under ID
          then save
          
          
                   
- Click on the PipeLine, select configure
          
          On Build Steps, Click on Build Steps, select execute Sonarqube Scanner
          On Analysis Properties, paste the project key on the space, then save
          
![sonar-project-key](https://user-images.githubusercontent.com/101070055/234584342-c702a6dc-5d66-48f6-a6b5-81eaa0dbad10.png)

- Build the PipeLine to run code analysis
          
![sonar-success](https://user-images.githubusercontent.com/101070055/234584941-986070cc-0fae-43b5-80b1-89be35d672b1.png)

![code-passed](https://user-images.githubusercontent.com/101070055/234598718-66eb61fa-015e-4921-b7d6-02a1d6f5c0a8.png)
        
# Configure Docker Server
          
         Sudo apt update -y
          sudo apt install docker.io
          
- I will configure Server, and ensure I cann ssh from jenkins to docker server
          
- Edit SSH config of docker-server
          
          sudo nano /etc/ssh/sshd_config
          uncomment "PubkeyAuthentication yes"
          Set PasswordAuthentication yes
          save and exit
          restart sshd service (systemctl restart sshd

- Set Docker Server User password ( change to root user mode)
          
          sudo su
          passwd ubuntu
          New password: <enter any choice> hit enter
          retype password, hit enter

- On jenkins Server, Switch to root user mode
          
          sudo su jenkins
          ssh ubuntu@<docker-IP>  (it will prompt to enter docker user password) and it will be successfull

- Generate Public and private key
          
          ssh-keygen  (hit enter till end)
          
- Copy ssh-key-id with name of the server
          
        ssh-copy-id ubuntu@3.80.44.226  
        Enter docker server password
        Now, i can ssh from jenkins server to docker server without password prompt
        ssh ubuntu@<docker-Server-IP>

![ssh](https://user-images.githubusercontent.com/101070055/234597542-b7903b31-f857-4a07-9b40-5b7ec12d6086.png)

- On docker server add Current User to Docker Group to be able to execute docker command without sudo
          
          sudo usermod -aG docker ubuntu
          newgrp docker
          mkdir website
          
# Integrate Docker Server on Jenkins
          
- Click on manage jenkins, select System
          
          Scroll Down to Server Group center,
          On server group list, click on add
          Enter Server name, port, username and password of the remote server
          apply and save

![server-group](https://user-images.githubusercontent.com/101070055/234599886-cf4f618e-ad94-4d59-970b-23b4b24f95a3.png)
     
- Add Server List, 
          
          On cofigure system, select server group
          Enter Server name
          and enter server public IP
          apply and save
          
![server-list](https://user-images.githubusercontent.com/101070055/234603781-c468e478-6ea9-4bc4-844f-1ef049c9c93b.png)

- Create a Dockerfile from github/ locally then push to github    
          FROM nginx
          COPY . /usr/share/nginx/html
          
          save and exit
          
- Click on PipeLine, Select configure, to copy all the website content to docker server
          
          Under Build Steps, select Execute shell
          
          scp -r ./* ubuntu@<docker-Server-IP>:~/Website/

- Click on Build Steps, select remote shell
          
          cd /home/ubuntu/website
          docker build -t website .
          docke run -d --name Web-Site -p 3000:80 website
          
![run-command](https://user-images.githubusercontent.com/101070055/234614782-96274e82-5ef5-4d9d-9a30-b0cdb388ce79.png)

-  save and build the pipeline.   
          
If build is successfull, the website should be running on docker server
          
![dockr-ruh](https://user-images.githubusercontent.com/101070055/234623184-32f1d9a6-fb7c-4759-bf61-153e938ad2f2.png)

I can reach the website now through port 3000.
          
![site-up](https://user-images.githubusercontent.com/101070055/234623774-148f3bdb-91a4-4b59-929b-04e4451af491.png)

# Completed
          
   ![CICD-1](https://user-images.githubusercontent.com/101070055/234623954-0d691e3a-518a-470d-ab6e-7af6a854a653.png)
       
