

# JENKINS.

Jenkins is an open source automation server written in Java. It is used to automate all sorts of tasks such as building, testing, and deploying software. Jenkins can be used to automate tasks related to building, testing, and deploying software. It can be used to monitor executions of externally-run jobs, such as cron jobs, and can also be used to schedule jobs to run at specific times.

The main focus of Jenkins is to provide an easy-to-use and extensible automation platform, which can be used to build, test and deploy applications. Jenkins is designed to be highly extensible, and there are a wide range of plugins available that can be used to extend its functionality. Jenkins supports many different version control systems, such as Git and Subversion, and can be used to automate the process of building, testing, and deploying applications.

## Jenkins CICD.

![jenkins](https://user-images.githubusercontent.com/101070055/232617297-f6da8b83-06cd-4163-8045-030d1155c6ba.jpeg)

![cicd](https://user-images.githubusercontent.com/101070055/232618178-872eb4fd-eba6-4ba9-9c6f-ad3572a3e46f.jpeg)

Jenkins CICD (Continuous Integration and Continuous Delivery) Pipelines are a set of automated processes used to build, test, and deploy applications. These pipelines are triggered by changes made to the source code and produce a set of instructions that help developers understand the exact steps necessary to get their applications ready for production.

A CICD Pipeline typically consists of five stages: Build, Test, Deploy, Monitor, and Release.

1. Build: The Build Stage is where the source code is compiled and packaged. This could be done with a variety of development tools such as Ant, Maven, or Gradle. The Build Stage also includes checking the code for errors, creating version tags, and pushing code to a version control system.

2. Test: The Test Stage is where automated tests are run to ensure the software meets the desired criteria. Unit tests, functional tests, and integration tests can all be part of the Test Stage.

3. Deploy: The Deploy Stage is where the application is deployed to an environment such as a staging or production server. The Deploy Stage can involve setting up the environment, creating databases, configuring permissions, and more.

4. Monitor: The Monitor Stage is where performance metrics are gathered and reviewed to determine if the application is meeting the desired objectives. This could involve collecting CPU and memory usage, user activity, and other metrics.

5. Release: The Release Stage is where the application is made available to the public or to a specified user group. This could involve updating the application in an app store or website, or pushing a new version out to a customer base.

# Installing jenkins server

There are more than ways of which i can deploy jenkins server.

 - Jenkins Docker image.
 - Jenkins terminal installation, Locall installation.

In all of this project, I will be using local installation of jenkins server on AWS EC2 Uvuntu instance

# prerequisites

- Running Windows/Linux Machine, I am personally using AWS EC2 Ubuntu Instance
- Running Java Environment

Install java on Ubuntu/Linux machine

         sudo apt install openjdk-17-jdk -y

Download jenkins required package and its dependencies with curl command

 curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
          /usr/share/keyrings/jenkins-keyring.asc > /dev/null
        echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
          https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
          /etc/apt/sources.list.d/jenkins.list > /dev/null

        sudo apt-get update
        sudo apt-get install jenkins 

Jenkins Default Port = 8080

