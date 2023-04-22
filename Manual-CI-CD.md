
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

- I will launch two different AWS EC2 Ubuntu instance, for CI (Continuos integration) server, and sonarQube for quality code analysis. All the cloning / packaging, compiling and building will be done on CI server, while the quality code analysis will be done with different server.

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

            sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.0.0.68432.zip
            unzip sonarqube-10.0.0.68432.zip
            sudo apt install openjdk-17-jdk
            Start the sonarqube server

- Access the web GUI Sonarqube server with server IP address along with port 9000
    
        http://<Ip-Addess>:9000

        Generate a token, in order to run a code analysis command from Ci server
        
![generate-token](https://user-images.githubusercontent.com/101070055/233754468-66342b55-ec44-4e0f-be52-f49df37129c4.png)
  
- To run Manual code analysis from CI server to Sonarqube, i need to integrate sonar-scanner plugin on maven

              <plugin>
                <groupId>org.sonarsource.scanner.maven</groupId>
                <artifactId>sonar-maven-plugin</artifactId>
                <version>3.9.1.2184</version>
              </plugin>

- On CI server, i have edited pom.xml file, and have added sonar-maven-plugin to it.

![plugin](https://user-images.githubusercontent.com/101070055/233752454-ef170a06-481d-4bf7-b124-0cc04b01f2af.png)

- Check code quality from CI server command

        mvn sonar:sonar -Dsonar.host.url=http://<sonar-server-IP-Add>:9000 -Dsonar.login=<generated-token>
        
# Step 3 (Run sonarQube code analysis)

- Run code analysis command from Ci Server, where i have maven installed

        sudo mvn sonar:sonar -Dsonar.host.url=http://52.91.97.102:9000 -Dsonar.login=sqa_88549801c9f5bfc49b7832651ef4d1fb94d96079

![analysis](https://user-images.githubusercontent.com/101070055/233754916-b4430b73-d695-4a2b-b406-762310bde634.png)

- If if the code quality security analysis is passed, it will show "passed"
 
 ![passed](https://user-images.githubusercontent.com/101070055/233755000-5bf72c13-d49d-4865-831c-b9be4b47127b.png)

# Step 4 (Create Dockerfile)

- Now my code quality analysis is checked and passed, i will create a dockerfile with the generated artifact after building, packaging and compiling the code with maven.

                touch Dockerfile
                vim Dockerfile

                FROM openjdk:17

                COPY target/*.jar app.jar

                ENTRYPOINT [ "java", "-jar", "app.jar" ]
~                                           
![java](https://user-images.githubusercontent.com/101070055/233755379-12f82f1e-1b10-4bb0-bcbc-003880174ff1.png)

# Step 5 (AWS ECR)

- Create Aws ecr repository

![ECR](https://user-images.githubusercontent.com/101070055/233755812-236bf0cf-820d-482f-b229-0ab3b9711243.png)

![Ecr-created](https://user-images.githubusercontent.com/101070055/233755821-5de8d0f5-26d3-4257-8c26-a4f5e7dc0792.png)

- To be able to authenticate and push image to aws ecr, i will create a role/user with only ecr administrative role

- To authenticate , click on view command on aws ecr, 

![ecr-auth](https://user-images.githubusercontent.com/101070055/233756614-23717159-7bf7-46b3-a891-5551ef446a39.png)

- Follow the steps 

- To authenticate my CI server to aws ecr

       aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 700930115870.dkr.ecr.us-east-1.amazonaws.com
       
![ecr-authy](https://user-images.githubusercontent.com/101070055/233756897-1eae0b54-3d69-42a7-bd9d-3f44320507f8.png)
       
- Build Image from Dockerfile

            docker build -t boot-app .

![image-build](https://user-images.githubusercontent.com/101070055/233756955-064013a2-bc8f-4a11-a62d-39a7aed58978.png)

- Tag the image

            docker tag boot-app:latest 700930115870.dkr.ecr.us-east-1.amazonaws.com/boot-app:latest
            
- Push the image to aws ecr

            docker push 700930115870.dkr.ecr.us-east-1.amazonaws.com/boot-app:latest 
            
![push-image](https://user-images.githubusercontent.com/101070055/233757079-77464918-1bd3-4b48-8ea8-e2d6be073d60.png)

- The image is now available on aws ecr

![push-image](https://user-images.githubusercontent.com/101070055/233757170-4460f8e3-9789-4c88-b841-231ae07327ae.png)

