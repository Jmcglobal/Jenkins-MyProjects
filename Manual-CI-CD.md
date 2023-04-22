
## Overview

![cicd-manual](https://user-images.githubusercontent.com/101070055/233744290-47f0460f-c264-42af-b3a8-1961a9a8a1a8.png)

# As a DevOps Engineer, working with team of software Engineers, there is ongoing project code on github repository, while the software engineers is commiting the codes, DevOps engineer should be doing the following.

- Pulling the codes from central repository.
    
- Building, packaging and compiling the codes
- Testing the codes for, and performing quality code analysis with sonarqube
- If the code, have passed a quality check, containerised the code with docker 
- Push the code image to private repository (dockerhub/ECR).
- From the image repository, deploy the code to test Server,
- Test the performance of the application, if all client requirement is met,
- If the client requirement of the software ia all met, inform the client before the releasing the software on production environment, or automatically deploy the code to production environment

 -   - When the software is running on production environment, monitor the software performance and resources it is consuming, the traffic burst and and for any bugs...

# Manual CICD for simple demonstration

# step1

NOTE: As DevOps Engineer, it is of best practices to understand the kind of language the software wll be build on, this will help in debug and be able  trace and interpret any error or bug when compiling, testing, and packaging the code.

- i will launch two different AWS EC2 Ubuntu instance, for CI (Continuos integration) server, and sonarQube for quality code analysis. All the cloning / packaging, compiling and building will be done on CI server, while the quality code analysis will be done with different server.

![Server-launched](https://user-images.githubusercontent.com/101070055/233749585-cfe50ae3-5dce-4745-85ee-e6dd30073bcc.png)

- Ci Server prerequisite tools installation
        java (openjdk-17-jdk)
        Git
        Maven
        Docker

- checkout the project (Sample Spring boot application)

        git clone https://github.com/Jmcglobal/Spring-app
        
![clone-code](https://user-images.githubusercontent.com/101070055/233749993-6440344f-5871-4d00-8c3b-43d121c5f41b.png)

- Use maven to build, compile and package the code (change dir to the project folder)

       sudo mvn clean package
       
- once the code have been build, compiled and packaged, an extra folder is created, inside the folder (target), i can find the code artifact id, and reports . 

![target](https://user-images.githubusercontent.com/101070055/233750868-7f39f48e-ca14-428c-ac99-ca308562406a.png)

# Step2 (SonarQube)

- Install SonarQube (by default, sonarqube is running on port 9000, therefore it should be opened on the security group attached to the Server)


