
# Platform Engineer: Technical Tests 

Question 1:
Your task will be to document the steps to create a pipeline that will build a Maven java spring boot application (A simple hello world app will suffice) as well as build a Docker image and store it into a remote repository, thereafter deploy that image.  
We would like to give you the opportunity to share your thinking, so feel free to submit notes on your implementation and the choices you have made. It is key that your notes depict a setup that is reproducible and easy to follow by someone less knowledgeable.


Acceptance criteria:
At the end of the task, we expect that:
- The application has been appropriately configured.
- The deployment process is repeatable.
- There's enough documentation/notes on your thinking and how to run/test your submission.

Please keep in mind that the main objective is not correctness, but rather a way for us to assess how you think through the steps taken.

Optional / Bonus Criteria:
- Produce the hello world source code, Docker file, as well as the pipeline in YAML format. Please submit via a GitHub repository or zipped folder

Question 2:

Just because a process can be automated does not necessarily mean that it should be automated. What criteria would you consider when evaluating a process that could be automated?

Question 1:

You have an existing platform built locally that is hosting your online web applications.
The platform consists of the following layers:
- a web server hosted on Windows IIS
- a middleware server hosted on RHEL
- a database server hosted on Oracle
The web application is publicly accessible and is designed to meet the principles of a highly scalable, available, performant and secure platform.
Your team regularly runs tests against the platform ensuring it meets functional, performance and security requirements.
Explain with as much logical and technical detail as possible how you would approach and provision a lift and shift of the platform to AWS. 
Your explanation needs to show the design of the platform in AWS as well as detail the steps required to setup and configure it with the objective of handing over a working platform that meets the same principles but hosted in AWS. In addition, how your team would re-assess functional, performance and security requirements. 
Below are further details of the on-premises hosts.
Layer
OS Version
Cores
Memory in Gb
Comments
Web	Microsoft Windows Server 2012 R2 Standard	4	8	X2 Nodes
Middleware	Red Hat Enterprise Linux Server release 7.9	4	62	X2 Nodes
Database	Oracle	28	168	X2 Nodes

Question 2:

The call centre has logged several incidents regarding the user experience of the platform running on-premises. 
It was identified the negative experience is due to the signification number of slow backends. 
Knowing this prior to the lift and shift question 1, how would you plan to solve these shortfalls when you migrate to AWS?
What actions can be taken to improve the latency issues?

Question 3:
There is a valuation business process on-prem which uses a crontab shell script to export data from the transactional database to an attached storage share. The tables are then imported into a different reporting database.
What solution would you propose to replace the existing on-prem solution in AWS RDS.






## Question 1: Docker Maven java spring boot application

Repo: https://github.com/Takapiwanashe/platform-engineer-assesment  

1. Create a Repository on github.
2. Clone the remote repository on from github to local machine.
```bash
  git clone https://github.com/Takapiwanashe/platform-engineer-assesment.git
```
3. Create the sample Maven java spring boot application in a directory spring-boot-main
```bash
  mkdir spring-boot-main
```
4. Place the application files inside the spring-boot-main I used a sample application generated from https://start.spring.io/.
5. Change directory to the working directory which is spring-boot-main
```bash
  cd spring-boot-main
```
6. Change from to the complete directory and test if the application can run locally using
```bash
  ./mvnw spring-boot:run
```
7. After the command above open new terminal window and run the service with curl to check
```bash
  curl localhost:8080
```
You may also use a browser for this.
8. Now create a new jar file using maven builder.
```bash
  ./mvnw clean package
```
9. After this create a docker file on the spring-boot-main directory (Dockerfile in repo)
10. create a docker image by using the docker build command
```bash
  docker build -t takapiwanashe/spring-boot .
```
11. Create a docker container by running
Create a docker container by running
```bash
  docker run -d -p 8086:8080 --name spring takapiwanashe/spring-boot:latest
```
12. Navigate to http://localhost:8086/ in your browser to view the spring boot application.
13. Now that the application is running on Docker on a local environment we need to store it into a remote repository and deploy that image. I used github and github actions to deploy the application to Dockerhub
14. Go to the github repositorty and open the repository Settings, and go to Secrets > Actions.
14. Create a new secret named DOCKERHUB_USERNAME and your Docker ID as value.
15. Create a new for Docker Hub.
16. Add the PAT as a second secret in your GitHub repository, with the name DOCKERHUB_TOKEN.
17. Create the main.yml in .github/workflows
18. Commit the yaml
19. The yaml on the repo will save the image on Dockerhub.
20. The container can be used by:
```bash
  docker pull takapiwanashe/spring-boot:latest
```
21. To run
```bash
  docker run -d -p 8086:8080 --name spring takapiwanashe/spring-boot:latest
```
22. This can be deployed to EC2 by pulling the image and running it


## Question 2: Automation

Human Factor:

Which processes take up a large percentage of my employees’ time?
Which processes require a high percentage of dedicated staff?
Which processes require hiring of additional staff during seasonal spikes in workload

Complexity:

How many steps are involved in the process (both user steps and integration steps)?
Does it include critical functions?
How many different applications does the process use end-to-end?
Are there decision-points within the process that require human intervention?
What is the estimated delivery time to develop the automation scenario?

Stability:

How often does the process get changed?
How much does it change?
What is the lead time for change?

## Question 1: Online web application implementation.

For this implementation I will set up a VPC (Virtual Private Cloud) I will assign it a CIDR block of 10.0.0.0/16. After setting up a VPC I will create an Internet gateway. After creating the Internet gateway I will have to attach it to the VPC this will allow for communication  between the EC2 instances in the VPC and the internet. I will then  create 4 subnets this will be a way to  group the  resources in the VPC with their IP ranges I will have 2 public subnets and 2 private subnets with the following CIDR blocks subnet1: CIDR (10.0.1.0/24) Availability Zone (us-east-1a), subnet2: CIDR (10.0.2.0/24) Availability Zone (us-east-1b), subnet3: CIDR (10.0.3.0/24) Availability Zone (us-east-1a), subnet4: CIDR(10.0.4.0/24) Availability Zone (us-east-1b). After this i will proceed to create 2 route tables the public route table and the private route table. The public subnet will be associated with the public route table and  the private subnet will be associate with the private route table. After this i will create a NAT Gateway this will allow the EC2 instances in the private subnet to have access to the internnet. After this i will create an Elastic Load balancer The essence of the load balancer is to distribute load across the EC2 instances serving the application (EC2 Instance with Windows IIS web server).To enable the EC2 instances I will create an Auto Scaling Group with common configuration for instances thus my Launch configuration will be having configuration for aweb server hosted on Windows IIS (in public subnet) and a middleware server hosted on RHEL (In private subnet). I will also create an RDS database in the private subnet and choose oracle as the Engine Type. For continous monitoring of the architecture I will configure cloud watch metrics for each of the resources for EC2 i woul enable detailed monitoring so as to get fine grained metrics on how the instannces are performing. I will also make sure that all the resources configured have the right IAM permisions configured were resources need to communicate with each other i will make use of IAM roles instead of IAM users

## Question: 2: Call centre incidents

To resolve the issue of slow backends I will implement a Load Balancer as I have stated above this will enable traffic to be routed to. I will make sure that my instances are  always running at least 8 cores and have more VCPUs to increase performance. I will monitor RAM usage before migration and make sure I have allocated enough RAM on the Instances. I will also use SSD-based volumeswith Provisioned I/O  I will also make sure that the instances which communicate together frequently are in the same AZ so as to reduce latency

## Question 3: Crontab shell

The solution to this I would use a Lambda function to run the crontab shell scripts and transfer the data to RDS.

