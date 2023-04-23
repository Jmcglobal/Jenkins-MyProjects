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
 
 - configure Maven 

i need to integrate maven on jenkins, therefore i will add maven home directory on jenkins, so that jenkins will make use of it, when building, compiling and packaging the project.

     This is done on jenkins global tools configuration

![add-maven](https://user-images.githubusercontent.com/101070055/233813078-319fba91-4be2-400b-bb44-fc78a0ab8551.png)

# pipeline to checkout and building the project

![jenkins-maven-pipeline](https://user-images.githubusercontent.com/101070055/233813131-911701d4-9ce2-48d5-9722-9df9340fdf09.png)

In this project, i will be using declarative pipeline

                        pipeline {
                            agent any 

                            tools {
                                maven "maven:3.6.3"
                            }

                            stages {
                                stage ("Checkout the project") {
                                    steps {
                                        git branch: 'master', url: 'https://github.com/Jmcglobal/Spring-app.git'
                                    }
                                }

                                stage ("Build The package") {
                                    steps {
                                        sh "mvn clean package"
                                    }
                                }
                            }
                        }
                        
apply and save 

Once the pipeline is executed, the Spring-app project will be cloned from the GitHub repository and built using Maven.

# Next step. (configure sonarqube server)

I will use Sonarqube docker image here

- Installations

        sudo apt update -y && sudo apt upgrade -y
        sudo apt install docker.io
        
- Run SonarQube image as container

       sudo docker run --detach --name sonarqube -p 9000:9000 sonarqube:9.9.0-community
       docker ps
       
![sonar-run](https://user-images.githubusercontent.com/101070055/233815092-20883295-4a44-4c2b-aaaf-6fcddfb9cbce.png)
       
- Generate a token from sonarqube 

![token](https://user-images.githubusercontent.com/101070055/233815591-dd5fee79-bf2b-4d7e-88f2-30a7bb4cd5ce.png)
      
- Add plugins on Jenkins 

        SonarQube Scanner

![sonar-plugin](https://user-images.githubusercontent.com/101070055/233815688-7e936888-be0e-4b72-91c5-89f845a2e4bc.png)

- Add SonarQube Credentials on Jenkins

![sonar-credential](https://user-images.githubusercontent.com/101070055/233815797-727fb40b-2097-498d-81fc-15f2a5f20337.png)

Under secret, paste the generated token, and token name as ID, select secret text under kind

- Add Sonarqube server on jenkins

![sonar-server](https://user-images.githubusercontent.com/101070055/233816149-24556960-fd9b-44df-9582-91e2192fc565.png)

- Add pipeline for sonarqube

                        stage ("SonarQube Quality code check analysis") {
                            steps {
                                script {
                                withSonarQubeEnv (installationName: 'Sonarqube', credentialsId: 'token') {
                                sh 'mvn sonar:sonar'
                                }
                                }
                            }
                        }

At this stage, i have added, sonarqube pipeline to run quality check sonarqube server ..

![sonar-success](https://user-images.githubusercontent.com/101070055/233816852-df5959f4-f044-414e-8dd0-295fdae65068.png)

- Adding quality gate pipeline to determine success or failed check

                        stage ("SonarQube Quality code check analysis") {
                            steps {
                                script {
                                withSonarQubeEnv (installationName: 'Sonarqube', credentialsId: 'token') {
                                sh 'mvn sonar:sonar'
                                }
                                timeout(time: 1, unit: 'HOURS') {
                                    def qg = waitForQualityGate()
                                    if (qg.status == 'Ok') {
                                        success 'Quality gate passed: $(qg.status)'
                                    } 
                                }
                                }
                            }
                        }

- Add Webhook on sonarqube

Click on the administration, select webhook

![webhook](https://user-images.githubusercontent.com/101070055/233817596-aef06354-86cd-438e-8518-21519e23bb10.png)

![quality-gate-success](https://user-images.githubusercontent.com/101070055/233819369-35a68bf7-9d1f-4edf-8274-45a9992d29f4.png)
