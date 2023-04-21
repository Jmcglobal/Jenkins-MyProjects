
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

- clone code ffrom Github repository

