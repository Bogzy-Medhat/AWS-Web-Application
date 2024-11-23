


#Building a Highly Available, Scalable Web Application

#Objective:
•	Create an architectural diagram: Illustrate the interactions between AWS services.
•	Estimate costs: Use the AWS Pricing Calculator for cost estimation.
•	Deploy a functional web application: Host on a virtual machine backed by a relational database.
•	Ensure high availability and scalability: Implement load balancing and auto-scaling.
•	Secure the application: Configure network security and manage access permissions.

#Requirements:
•	Functional: The solution meets the functional requirements, such as the ability to view, add, delete, or modify the student records, without any perceivable delay.
•	Load balanced: The solution can properly balance user traffic to avoid overloaded or underutilized resources.
•	Scalable: The solution is designed to scale to meet the demands that are placed on the application.
•	Highly available: The solution is designed to have limited downtime when a web server becomes unavailable.
•	Secure:
o	The database is secured and can’t be accessed directly from public networks.
o	The web servers and database can be accessed only over the appropriate ports.
o	The web application is accessible over the internet.
o	The database credentials aren’t hardcoded into the web application.
•	Cost optimized: The solution is designed to keep costs low.
•	High performing: The routine operations (viewing, adding, deleting, or modifying records) are performed without a perceivable delay under normal, variable, and peak loads.
 
#Architecture:
 
This section provides an overview of the key AWS components used to build a highly available, scalable, and secure web application. Each component plays a crucial role in ensuring the application meets performance and security requirements, especially during peak usage periods. The architecture is designed following AWS best practices to optimize cost, scalability, and high availability.
•	Virtual Private Cloud (VPC): Establishes a secure network environment with isolated resources, including public and private subnets across multiple Availability Zones for redundancy.
•	Amazon EC2 Instances: Hosts web applications, providing compute power with flexibility.
•	Amazon RDS: Manages the relational database independently from the application servers. Configured with MySQL engine in a multi-AZ deployment for high availability.
•	AWS Secrets Manager: Secures sensitive information like database credentials, enabling secure access by the web application without hardcoding credentials.
•	Application Load Balancer (ALB): Distributes incoming traffic across multiple EC2 instances to ensure load balancing and high availability.
•	Auto Scaling Group: Automatically adjusts the number of EC2 instances based on demand to maintain performance.
•	AWS Cloud9: Provides a cloud-based development environment for managing and deploying code.
•	AWS WAF (Web Application Firewall): Protects the web application from common web exploits and bot traffic by filtering harmful requests before they reach the application.
•	Amazon CloudWatch: Monitors application performance and resource utilization, providing insights and alerts to maintain operational health and efficiency.
•	AWS Backup: Centralizes backup management for AWS resources, ensuring data protection and compliance through automated scheduling of backups for RDS and EC2 volumes.

Each component has been carefully selected and configured to ensure that the application remains responsive and secure, even under heavy load. The following sections will delve into the purpose and configuration of each component in detail.




![image](https://github.com/user-attachments/assets/98afcff0-0974-4e32-9570-ac28fc578830)

	

	 
1.	VPC
We selected the Virtual Private Cloud (VPC) to isolate the application within a secure network environment. This ensures that the web application is protected from unauthorized access and can only be reached through controlled entry points.

Components
•	Public Subnets: These are used to host resources that need to be accessible from the internet, such as load balancers or bastion hosts. Public subnets are configured with a route to an internet gateway, allowing inbound and outbound traffic.
•	Private Subnets: These host resources that do not require direct access from the internet, such as databases and application servers. Private subnets are configured without direct internet access, enhancing security by limiting exposure.

Availability Zones
Deploying subnets across multiple Availability Zones (AZs) ensures redundancy and high availability. If one AZ experiences an outage, the resources in other AZs can continue to operate, minimizing downtime.

Security Features
•	Network Access Control Lists (NACLs): These act as a firewall for controlling traffic in and out of one or more subnets. NACLs provide an additional layer of security at the subnet level.
•	Security Groups: These are used to control inbound and outbound traffic to AWS resources within the VPC. Security groups are stateful, meaning they automatically allow return traffic regardless of any outbound rules.

Benefits
•	Isolation: The VPC provides a logically isolated network environment for the web application.
•	Security: By using private subnets and security groups, sensitive components like databases are protected from direct internet access.
•	Scalability: The VPC can be easily scaled by adding more subnets or modifying existing configurations to accommodate growth.

Conclusion
The use of a VPC with both public and private subnets across multiple Availability Zones provides a robust foundation for building secure, scalable, and highly available applications on AWS. This setup aligns with the best practices outlined in the AWS Well-Architected Framework, ensuring optimal performance and security for the student records web application.
![image](https://github.com/user-attachments/assets/157e4fab-c359-4b4d-8fe1-9a0cde24bf33)

  
2.	EC2:
We utilized Amazon EC2 instances to host the web server and application code, providing the necessary compute power to run the application efficiently. These instances ensure that the application can handle varying loads by scaling up or down as needed.

Configuration
•	Operating System: The EC2 instances use the latest Ubuntu Amazon Machine Image (AMI). Ubuntu is chosen for its stability, security features, and wide support for various applications and development environments.
•	Software Installation: The setup script provided in JavaScript automates the installation of the required software and dependencies on the EC2 instances. This script ensures consistency across all instances, reducing manual configuration errors and speeding up deployment.

Key Features
•	Scalability: EC2 instances can be easily scaled to accommodate increased traffic, ensuring that the application remains responsive even during peak periods.
•	Flexibility: A variety of instance types and sizes are available, allowing you to choose configurations that best meet your performance and cost requirements.
•	Integration with AWS Services: EC2 integrates seamlessly with other AWS services such as Auto Scaling, which help enhance the application's scalability and availability.

Security
•	IAM Roles: Assign IAM roles to EC2 instances to securely access other AWS services without embedding credentials in your application code. This enhances security by managing permissions centrally.
•	Security Groups: Security groups Configured to control inbound and outbound traffic. Only allow necessary ports for web traffic (HTTPS) and database access (3306), minimizing exposure to potential threats.

Best Practices
•	Regularly update the AMI to ensure that all instances have the latest security patches and updates.
•	Use the monitoring tool ‘Amazon CloudWatch’ to keep track of instance performance and health.
•	Implement backup strategies for data stored on EC2 instances using ‘AWS Backup’.

Conclusion
Amazon EC2 provides a robust platform for hosting web applications, offering flexibility, scalability, and integration with other AWS services. By leveraging these capabilities, the student records web application can efficiently handle high traffic volumes while maintaining security and performance standards.
![image](https://github.com/user-attachments/assets/fa85f528-78d2-49a7-9c1d-d6fd5d545a60)

 

This is the web application using the dns name of  the EC2 machine
![image](https://github.com/user-attachments/assets/8ee4c45f-0687-408f-8e23-e0f4bc506057)

3.	RDS

Amazon RDS is used to manage the database independently from the web server, providing a managed database service that simplifies setup, operation, and scaling. By decoupling the database from the application server, it enhances the overall architecture's flexibility and reliability.

Configuration
•	Database Engine: The MySQL engine is chosen for its popularity, ease of use, and compatibility with many applications, especially the university’s admission platform that we are currently optimizing. It also offers a balance of performance and cost-effectiveness.
•	Multi-AZ Deployment: The database is configured to be hosted in a Multi-AZ deployment. This setup provides automatic failover to a standby instance in another Availability Zone if the primary instance fails. This configuration ensures high availability and data durability, which are critical for maintaining application uptime during peak admissions periods.

Key Features
•	Managed Service: RDS automates routine tasks such as backups, patching, and scaling, allowing developers to focus on application development rather than database maintenance.
•	Security: RDS provides options to encrypt data at rest and in transit. Additionally, access can be restricted using security groups and IAM roles to ensure that only authorized applications can connect to the database.
•	Performance Monitoring: RDS includes tools for monitoring database performance metrics, which help in optimizing queries and managing workloads effectively.

Security Considerations
•	Network Isolation: The RDS instance is placed in a private subnet within the VPC, preventing direct access from the internet. This setup enhances security by limiting exposure to potential threats.
•	Access Control: Only the web application is allowed to access the database through specific security group rules. This minimizes the risk of unauthorized access.
•	Secrets Management: AWS Secrets Manager is used to store and manage database credentials securely. This approach avoids hardcoding sensitive information in application code.

Best Practices
•	Regularly update the MySQL engine version to benefit from performance improvements and security patches.
•	We implement automated backups using ‘Amazon Backup’ to ensure data recovery in case of failure.
•	We used ‘read replicas’ to improve read performance and offload traffic from the primary database instance.
 
Conclusion
We used Amazon RDS to provide a robust solution for managing databases independently from application servers. By leveraging RDS's managed services, the web application can achieve better performance, security, and operational efficiency.
This configuration aligns with AWS’ best practices for building scalable and secure applications.
![image](https://github.com/user-attachments/assets/a65df209-a43d-47fb-a7b2-156059c357c5)

 
This is an example of adding students to the database 
 ![image](https://github.com/user-attachments/assets/e6f1cc92-72c1-4e77-a7d1-ac758fe94f7e)


 
4.	Secrets Manager And Iam

AWS Secrets Manager is used to securely store and manage sensitive information such as database credentials. This service helps eliminate the need to hardcode sensitive data in application code, enhancing security by reducing the risk of credential exposure.

Integration
•	Access by Web Application: The web application retrieves credentials from Secrets Manager at runtime. This approach ensures that credentials are not stored in the application code or configuration files, minimizing security risks.
•	IAM Roles: The EC2 instances running the web application are assigned IAM roles with permissions to access Secrets Manager. This setup allows the application to fetch secrets securely without embedding access keys in the code.

Key Features
•	Automatic Rotation: Secrets Manager will automatically rotate credentials according to a defined schedule, ensuring that they remain secure over time without manual intervention.
•	Audit and Monitoring: Integration with AWS CloudTrail allows for monitoring access to secrets, providing an audit trail for security compliance.
•	Encryption: Secrets are encrypted at rest using AWS Key Management Service (KMS), ensuring that sensitive data is protected.

Security Considerations
•	Least Privilege Access: IAM policies have been configured to grant only the necessary permissions for accessing specific secrets, adhering to the principle of least privilege.
•	Network Isolation: Ensure that access to Secrets Manager is restricted to authorized network paths within your VPC.

Best Practices
•	We are intending to regularly review and update IAM policies associated with accessing secrets.
•	Use automatic rotation features to keep credentials fresh and reduce the risk of compromise.
•	Monitor access logs through AWS CloudTrail for any unauthorized attempts to access secrets.

Conclusion
AWS Secrets Manager provides a robust solution for managing sensitive information securely. By integrating it with the web application, you ensure that database credentials are handled securely, aligning with best practices for application security and compliance.
Secret for db in Secrets manager
![image](https://github.com/user-attachments/assets/0f942197-f9c7-4905-b843-39d80354ad35)

The instance role permissions used for EC2
![image](https://github.com/user-attachments/assets/a8e3261d-51ae-47ec-bad6-1173491dcd16)

5.	Application Load Balancer

We used The Application Load Balancer (ALB) to distribute incoming traffic across multiple Amazon EC2 instances. This ensures that no single instance is overwhelmed with requests, which enhances the application's availability and reliability. By balancing the load, the ALB helps maintain optimal performance, even during peak traffic periods.

Configuration
•	Listener Configuration: The ALB uses listeners to check for connection requests from clients. We configured listeners to use HTTPS protocol, allowing for secure communication between clients and the application.
•	Target Groups: EC2 instances are registered in target groups, which the ALB uses to route requests. This setup allows for flexible scaling and management of instances without affecting the load balancer's configuration.

Key Features
•	Health Checks: The ALB performs health checks on registered instances to ensure that traffic is only routed to healthy instances. This feature helps maintain application uptime by automatically rerouting traffic away from unhealthy instances.
•	Security Features: We integrated with the AWS Certificate Manager (ACM) allows for easy management of SSL/TLS certificates, enabling secure communication over HTTPS.

Security Considerations
•	Security Groups: Configure security groups for the ALB to allow only necessary inbound traffic from clients and restrict outbound traffic to specific ports used by EC2 instances.
•	IAM Policies: Use IAM policies to control access to the ALB configuration and ensure that only authorized users can make changes.

Best Practices
•	We regularly monitor the load balancer metrics using Amazon CloudWatch to ensure optimal performance and quickly identify any issues.
•	Use AWS WAF (Web Application Firewall) with the ALB to protect against common web exploits and attacks.
•	Implement SSL/TLS termination at the load balancer level for secure communication without burdening backend instances with encryption tasks.

Conclusion
The Application Load Balancer plays a critical role in ensuring that the web application remains highly available and responsive under varying loads. By distributing traffic efficiently and providing advanced routing capabilities, the ALB enhances both performance and security in accordance with AWS best practices.
![image](https://github.com/user-attachments/assets/fe15c74f-ff82-40a7-bf6c-797e2dcda52d)

 
Reaching the web application through the Application Load Balancer DNS name
![image](https://github.com/user-attachments/assets/68e02b49-a3d0-4b84-a14e-8bf69407005c)

6.	Auto Scaling Group

The Auto Scaling Group (ASG) automatically adjusts the number of Amazon EC2 instances based on demand. This ensures that the application maintains optimal performance by scaling out during high traffic periods and scaling in during low traffic periods, thereby optimizing resource usage and costs.

Policy
•	Target Tracking Policy: This policy automatically adjusts the number of instances to maintain a specified metric, such as average CPU utilization. By setting a target value, the ASG ensures that the application performs efficiently under varying loads.

Configuration
•	Launch Template: Defines the configuration for EC2 instances, including AMI, instance type, and security groups. This template is used by the ASG to launch and terminate instances as needed.
•	Availability Zones: The ASG is configured to deploy instances across multiple Availability Zones to enhance fault tolerance and availability.

Key Features
•	Dynamic Scaling: Automatically adjusts capacity in response to changes in demand, ensuring that the application remains responsive and cost-effective.
•	Health Checks: Regularly monitors instance health and replaces any unhealthy instances to maintain application reliability.

Best Practices
•	Regularly review and adjust scaling policies based on application performance metrics.
•	Use Amazon CloudWatch alarms to trigger scaling actions based on custom metrics.
•	Monitor costs and adjust instance types or sizes as necessary to optimize spending.

Conclusion
The Auto Scaling Group ensures that the web application can handle fluctuating traffic efficiently while maintaining high availability and performance. By leveraging AWS's automatic scaling capabilities, you can optimize resource usage and costs effectively.
![image](https://github.com/user-attachments/assets/bb91cd23-980a-4ace-bf6e-873090808f47)



  
7.	Internet Gateway

An Internet Gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the internet. It serves as a bridge to connect the VPC to the internet, enabling resources in public subnets to receive inbound traffic from the internet and send outbound traffic to the internet.

Integration with Project Components
•	Public Subnets: The Internet Gateway is attached to the VPC and used to route traffic from public subnets. This setup allows resources such as the Application Load Balancer (ALB) and bastion hosts within these subnets to be accessible from the internet.
•	Application Load Balancer: The ALB, which is deployed in public subnets, uses the Internet Gateway to handle incoming requests from users accessing the web application over the internet.

Benefits
•	Internet Access: Provides necessary internet access for resources that require it, such as updating software packages on EC2 instances or allowing users to access web applications.
•	Scalability and Redundancy: The Internet Gateway is designed to be highly available and scalable, ensuring consistent performance even under high traffic loads.

Security Considerations
•	Route Tables: Ensure that route tables are configured correctly to direct internet-bound traffic through the Internet Gateway for public subnets while keeping private subnets isolated.
•	Security Groups and NACLs: Use security groups and Network Access Control Lists (NACLs) to control inbound and outbound traffic, ensuring only authorized traffic is allowed.

Conclusion
Integrating an Internet Gateway is essential for enabling internet connectivity for public-facing components of your architecture. It plays a critical role in ensuring that your web application can be accessed by users over the internet while maintaining security through proper configuration of network components.




  
8.	Cloud9

We used AWS Cloud9 to act as a cloud-based integrated development environment (IDE) that provides a convenient platform for running AWS Command Line Interface (CLI) commands and scripts. This environment is particularly useful for developing, testing, and deploying applications in the AWS ecosystem, as it comes pre-configured with essential tools and SDKs.

Instance Type
•	t3.micro: This instance type is chosen for cost efficiency. It offers a balance of compute power and memory suitable for development tasks without incurring high costs. The t3.micro instance is part of the AWS Free Tier, making it an economical choice for development purposes.

Key Features
•	Pre-configured Environment: AWS Cloud9 includes a wide range of development tools, including support for multiple programming languages, a built-in terminal, and integration with AWS services.
•	Collaboration: It allows multiple developers to collaborate in real-time on the same codebase, enhancing team productivity.
•	Direct Access to AWS Resources: With AWS Cloud9, we can directly access and manage AWS resources using the integrated terminal and CLI, streamlining the development workflow.

Security Considerations
•	IAM Roles: We assigned IAM roles to the Cloud9 environment to control access to AWS resources. This ensures that only authorized actions can be performed from within the environment.
•	Network Security: Cloud9 environment is configured within a secure VPC setup to limit exposure to potential threats.

Best Practices
•	Cloud9 environment is regularly updated to ensure it has the latest security patches and software updates.
•	We used the version control systems ‘Git’ within Cloud9 to manage code changes effectively.
•	Monitor usage and performance metrics to optimize resource allocation and cost management.

Conclusion
AWS Cloud9 provides a robust and flexible development environment that integrates seamlessly with AWS services. By using a t3.micro instance, we developed the application efficiently while keeping costs low. The platform's features support collaborative development and streamline workflows for deploying applications in the cloud.

  
9.	CloudWatch

We implemented Amazon CloudWatch as a monitoring and management service built for developers, system operators, site reliability engineers (SRE), and IT managers. It provides data and actionable insights to monitor applications, respond to system-wide performance changes, optimize resource utilization, and get a unified view of operational health.

Integration with Project Components
•	EC2 Instances: CloudWatch monitor EC2 instances for metrics such as CPU utilization, disk reads/writes, and network traffic. This helps in ensuring that the instances are performing optimally and can trigger scaling actions if needed.
•	RDS: It monitors database performance metrics such as CPU load, memory usage, and I/O operations. This is crucial for maintaining database health and performance.
•	Application Load Balancer: CloudWatch provides metrics on request counts, latency, and error rates for the load balancer, helping to ensure that traffic is distributed efficiently.
•	Auto Scaling: It has been used to monitor scaling activities and ensure that the Auto Scaling policies are working as intended.
•	AWS WAF: Monitor WAF logs and set up alerts for suspicious activities or rule violations.

Key Features
•	Alarms: Alarms have been set up to automatically notify us of performance issues or threshold breaches, enabling proactive management.
•	Logs: CloudWatch collect and monitor log files from EC2 instances and other AWS services. This helps in troubleshooting application issues.
•	Dashboards: Custom dashboards have been created to visualize key metrics from different AWS services in one place for easy monitoring.

Benefits
•	Real-Time Monitoring: Provides real-time insights into application performance and resource utilization.
•	Automated Responses: Integrates with AWS Lambda to automate responses to specific conditions.
•	Cost Management: Helps in optimizing resource usage by providing insights into underutilized resources.

Conclusion
Amazon CloudWatch is a comprehensive monitoring tool that integrates seamlessly with the AWS services used in this project. It provides the necessary insights and tools to ensure that the web application remains highly available, scalable, and high performin 
	


Final Task:

Perform a load test on the application to monitor scaling.
![image](https://github.com/user-attachments/assets/75ba8464-ba8a-4d8e-91df-5a84a6f392e2)

  
Conclusion
This implementation plan provides a structured approach to deploying a robust web application on AWS, ensuring it meets all functional, security, scalability, and providing an improved user experience during peak periods.

