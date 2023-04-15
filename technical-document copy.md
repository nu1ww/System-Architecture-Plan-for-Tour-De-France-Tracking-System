# Bicycle Racing Platform


## Project Summary
This product serves the purpose of managing and organizing bicycle races, while also enabling the tracking and scoring of individual athletes in each race. Additionally, it provides real-time updates on the race status and the live positions of all competitors.

## Biggest Challenges
One of the biggest technical challenges that this system faces is handling large number of consumers and scaling the system load. This challenge can be addressed by using microservices, Event driven architecture, which allows us to break down the system into smaller, more manageable components.

Another challenge is to collect and process data from multiple sources in real-time. This can be addressed by implementing a real-time data processing pipeline that can ingest data from multiple sources and process them in real-time

   - Scalability: The system infrastructure should be designed to use load balancing, caching, and horizontally auto-scaling technologies. also frontend website store deploy in bucket and distributed via AWS CloudFront
   - Database/Storage: The system should use a combination of relational and NoSQL databases. 
        - Relational databases can be used to store user information and race data, 
        - Non Relational databases can be used to store real-time race data.
   - Real time Data Feed: Mobile application should be designed to use GPS technology (get Latitude and longitude) and a reliable data transfer protocol such as WebSockets or RTCP.

## Technical Decisions

- Decision: Web based frontend user application build using ReactJS and deploy on AWS S3 and distributed through AWS CloudFront 
    - Reason: ReactJS allows for the creation of fast, dynamic with reusable components client side rendering. And distributing it through AWS CloudFront provides a highly scalable and cost 
    effective solution for hosting and delivering static web content with low latency and high performance.

- Decision: Build dockerized application for Web based back office administration
    - Reason: Docker makes it easy to scale your application horizontally by creating additional containers to handle increased traffic or load.

- Decision: Select React Native for mobile application
    - Reason: Because React Native is cross-platform support, code reusability, performance, development speed

- Decision: Select AWS Lambda functions with ServelessJS framwork for real time race tracker
    - Reason: Because AWS Lambda functions with the ServerlessJS framework is cost effective for these kind of solutions

- Decision: Implement ETL process using Lambda function 
    - Reason: This function trigger based on schedule to do ETL process store DynamoDB data to Aurora MySQL DB
    
- Decision: Use AWS SQS (FIFO)
    - Reason: when Lambda function receives a message, it can send it to an SQS (FIFO) queue, where it will be stored temporarily until it can be processed by the database store process. The database store process can then consume the messages from the queue in the order they were received, ensuring that messages are processed in the correct order
    
- Decision: Use AWS Cognito use as customer identity and access management (CIAM) throwout the system
    - Reason: AWS Cognito is a fully managed service that can scale to support large number of users without requiring any infrastructure management and Its easy to integrate with AWS API Gateway also it has social logins and MAF in build 

- Decision: Select two databases for this system
    - Reason: Considering behavior of the system Its better to go with Relational Database and Non Relational Database
        - Relational Database Management: Aurora MySQL
        - No SQl Database: AWS DynamoDB (that provides fast and predictable performance, seamless scalability, and automatic data replication and backup.)

- Decision: Use API Gateway communicate REST API/Websocket
     - Reason: because its more secure, We can easily link with aws cognito pool, and lambda functions. can configure throttle rate. caching, can handle large amounts of traffic and can scale to support millions of API requests per second. and its offers several security features, including authentication and authorization, encryption, and protection against common web attacks such as cross-site scripting (XSS) and SQL injection.

- Decision: Deploy on ECS Cluster
    - Reason: ECS Cluster simplifies the management of containerized applications by providing a centralized platform for deployment, monitoring, and scaling. It also integrates with other AWS services such as EC2, ECR, and CloudWatch, making it easy to set up and manage your entire application stack.
    
### High Level Architecture Design
The diagram belows shows the high level component diagram that could be used to fulfil the requirements.

1. Web based back office administration application
2. Real time race tracker server
3. Web based frontend user application
4. Mobile application for tracking racers

Each of these components will be responsible for different aspects of the system as explained below.

![High Level Architecture Diagram](/assets/System-Design-Digram-of-Bicycle-Racing-Platform.drawio.png)

1. Back Office Administration Application:
    - The application will be responsible for managing users, creating new races, and viewing statistics from past races. The application should be connected to a database to store user information and race data. The database could be a relational database such as MySQL and NoSQL db line AWS DynamoDB.
        - Cloud Services 
            - This application deployed on AWS ECS Fargate Cluster
            - REST APIs and Websocket are serve through AWS API Gateway 
            - AWS Cognito as customer identity and access management (CIAM) 
            - Application Load Balance (ALB)
            - DynamoDB to get statistic data
            - S3 bucket to store static contents
            - CloudWatch to setup monitoring save application and deployment logs

2. Frontend User Application:
    - The frontend user web application build using a React. The application will be responsible for allowing users to log in and check statistics from past races. The application will also allow users to track live races in progress. The frontend application should communicate with the backend application using RESTful APIs and Websocket. 
        - Cloud Services
            - The application will need to be deployed on a S3 bucket and distributed with Cloud Front
            - REST APIs and Websocket are serve through AWS API Gateway
            - S3 Bucket
            - Cloud Front for distribution
3. Real-time Race Tracker Server:
    - The application will be responsible for collecting data from the mobile application and distributing it to all spectators watching the race in the frontend user application. 
        - Cloud Service
        - AWS Lambda
        - AWS API Gateway for websocket endpoint of a two-way communication link.
        - AWS SQS for queue realtime location data 
        - DynamoDB to store real-time race data.
        - CloudWatch to setup monitoring save application and deployment logs

### Technology Stack
The proposed technology stack for each component is as follows:

1. Web-based Back-office Administration Application:
    - Language: PHP 
    - Framework: Laravel
    - Database: MySQL
2. Web-based Frontend User Application:
    - Language: JavaScript
    - Framework: ReactJS
    - Database: REST API
3. Mobile Application for Tracking Racers:
    - Language: JavaScript
    - Framework: ReactNative
4. Real-time Race Tracker Server:
    - Language: JavaScript
    - Framework: ServerlessJS, Deployed on AWS Lambda

## Deployment plan

##### Deployment plan utilizes below service 
- GitHub 
- Docker
- AWS Environment
- GitHub Actions (CI/CD) 
- Cloud Formation Infrastructure as Code (IaC) tools
- App Store and Play Store for a React Native application

Each repository must handle four different branches, namely development, stage, production, and features. The development branch should be linked to the development environment, while the stage branch should be linked to the stage environment, and the production branch should be linked to the production environment. 

All merge requests to the main branches must be reviewed through a pull request process.

**Web-based Frontend User Application via GitHub Action**
- Build push to the S3 bucket  --> Invalidate CloudFront --> Deployment success or fail trigger via notification channel.

**Back Office Administration Application via GitHub Action**
- Build docker image and push to the AWS ECR -> Push to to ECS --> Deployment success or fail trigger via notification channel.

#### Guideline
- Initiate project repositories on GitHub 
    - Back Office Administration Application
    - Web-based Frontend User Application
    - Mobile Application 
    - Real-time Race Tracker applications for 3 lambda function

- Build Infrastructure using AWS Cloud formation
    - Write CF scripts for build all infrastructure items AWS VPC, ALB, ECR, ECS, RDS, API Gateway etc. 

- Write GitHub Actions Scripts for (CI/CD) :
    - Set up a GitHub Actions workflow to automate CI/CD process. 

- Build React Native application deploy to a relevant environment

- Set up monitoring and alerts:
    - Use a combination of monitoring tools AWS CloudWatch, Sentry.io, for incident notification use such as AWS SNS, Slack.

## Runtime Cost Analysis

Sharing a cost analysis based on a request rate of 1000 requests per second and 10,000 requests per second.

- Lets assume system get Request 1000/1Sec :
    - Monthly: 394.81 USD
    - Annually: 4,917.72 USD
    - Detail Estimation : https://calculator.aws/#/estimate?id=2f7badf2aa453d89a4dd93dbb8c8765214123705

- Lets assume system get Request 10000/1Sec: 
    - Monthly: 628.97 USD
    - Annually: 7,727.64 USD
    - Detail Estimation : https://calculator.aws/#/estimate?id=a7775372071497dc3d0dfde4eeae0f9a34a14500



--------------------------------------
![Scopic Software](/assets/footer.png)
