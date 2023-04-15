# Bicycle Racing Platform


## Project Summary
This product serves the purpose of managing and organizing bicycle races, while also enabling the tracking and scoring of individual athletes in each race. Additionally, it provides real-time updates on the race status and the live positions of all competitors.

## Biggest Challenges
One of the biggest technical challenges that this system faces is handling x number of consumers and scaling the system load. This challenge can be addressed by using microservices, Event driven architecture, which allows us to break down the system into smaller, more manageable components.

Another challenge is to collect and process data from multiple sources in real-time. This can be addressed by implementing a real-time data processing pipeline that can ingest data from multiple sources and process them in real-time

1. Scalability: The system infrastructure should be designed to use load balancing, caching, and horizontally auto-scaling technologies. also frontend website store deploy in bucket and distributed via AWS CloudFront
2. Database/Storage: The system should use a combination of relational and NoSQL databases. Relational databases can be used to store user information and race data, while NoSQL databases can be used to store real-time race data.
3. Real time Data Feed: Mobile application should be designed to use GPS technology (get Latitude and longitude) and a reliable data transfer protocol such as WebSockets or RTCP.

*[TODO: Please use this section to identify the biggest challenges that you see and solutions that you would implement for them]*

## Technical Decisions

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
    - Framework: Serverless JS 

## Deployment plan

3. Mobile Application:
The mobile application should be built for both iOS and Android devices using native mobile development frameworks such as Swift and Kotlin. The application will be responsible for tracking the current position of the racer and sending it to the real-time server. The mobile application should use OAuth2 authentication protocol to ensure only registered users can access the application. The application will also need to use GPS technology to track the racer's position.


## Runtime Cost Analysis







--------------------------------------
![Scopic Software](/assets/footer.png)