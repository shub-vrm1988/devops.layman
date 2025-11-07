1. Created a VPC named as wordpress-vpc with CIDR as 10.0.0.0/24

![alt text](Screenshots/image-51.png)

2. Created 2 Public, 2 Private, 2 Database Subnets, by dividing 8 equal subnets under the wordpress-vpc with each 1a/1b availability zone under the region us-east2

![alt text](Screenshots/image-52.png)

3. Created 2 Route Tables under the wordpress-vpc

![alt text](Screenshots/image-53.png)

4. Created a Internet Gateway and attach it to wordpress-vpc. Associated the private and public subnets to private & public route tables respectively.

![alt text](Screenshots/image-54.png)
![alt text](Screenshots/image-55.png)

5. Edited the public route table and attached the internet gateway so that public subnet can connect to outside world via Internet. 

![alt text](Screenshots/image-56.png)

6. Created a EC2 Instance in one of public subnet under same vpc and security group. However, initially noticed issues while doing SSH. Later we checked that security group attached to EC2 should have a inbound rule for port 22. Basically to connect to your instance from the internet, you need an inbound rule for port \(22\). To connect from your instance to the internet, you need an outbound rule, typically allowing all outbound traffic or specific ports like \(80\) for HTTP & \(443\) for HTTPS(If repos defined are secure links). Post updating the security groups, able to ssh into the instance and did a successful patch. As a check, we have stopped and started a public EC2 instance and noticed that private IP address is hardcoded. However public ip address is changed.

![alt text](Screenshots/image-57.png)
![alt text](Screenshots/image-58.png)

7. For a Private EC2 Instance, I tried doing SSH or connect via virtual console but failed. NAT Gateway will be host in public subnet and create a route table for private subnets and attach it to NAT gateway. A NAT (Network Address Translation) Gateway allows instances in a private subnet to access the internet or external services without exposing them to unsolicited inbound connections. By translating private IP addresses to a public IP address, it provides outbound connectivity for tasks like software updates. NAT Gateway requires a Elastic IP Address, which is a static, public IPv4 address that can be associated with a cloud instance and remapped to different resources within the same region, providing fault tolerance and high availability by allowing quick redirection of traffic when an instance fails.

Created my blog: https://medium.com/@shub.vrm/a-simple-guide-for-understanding-and-creating-a-vpc-in-aws-99421d1e3c8b


8. Created a Postgresql database free tier database with our vpc and security group. Make sure to give database a name otherwise it would be taking a default name. E.g: for mine, it will take default.postgres17. Post created, I have modified the subnets to use wordpress-rds1 & 2. 

**Amazon RDS DB subnet group must have subnets in at least two different Availability Zones (AZs) to provide redundancy, even for single-AZ instances. To fix this, you need to create and add at least one more subnet to your DB subnet group, ensuring it's in a different AZ (e.g., us-east-2b) from your current subnet (us-east-2a). You can also create subnets in more than two AZs to further enhance failover capabilities.

A few suggestions came from AWS:
![alt text](Screenshots/image-59.png)

Outcome from Step 1-8
![alt text](Screenshots/image-60.png)

9. Amazon ECS (Elastic Container Service) is a fully managed container orchestration service by Amazon Web Services (AWS) that automates the deployment, management, and scaling of containerized applications, allowing users to run Docker containers without managing the underlying infrastructure. It offers a serverless option (AWS Fargate) for running containers on Amazon-managed infrastructure, as well as the option to run on a cluster of your own Amazon EC2 virtual machines for more control.

It has template called as Task Definitions with a wrapper as Tasks & runs multiple containers, talking to each other. Container can run once & die. However, if we want our container to never die and always serve traffic, then we need to use service, which will make sure container never dies, automatic scaling, auto upgrade & if it dies by mistake, container should come up. Then we should minimum 1 tasks or run multiple tasks for scaling. 

10. Created a ECS cluster with AWS Fargate(Serverless) Infra and defined tasks definitions with serverless infra and Task role & Task Execution role

11. With a IAM user "devops.layman" with Admin access, created access & secret key id & pushed my docker image to AWS ECR. Detailed steps defined in below file. We are connecting to ECR internally. Because we do not want to go via Internet and that is the reason we do not have a public ip address.

configure-aws-IAM.md

12. Created Task Definition using my docker images from ECR, env variable from docker file and value from database - postgresql://postgres:admin1234@wordpress-db.c56ucwigqtr5.us-east-2.rds.amazonaws.com:5432/wordpressdb

13. Under ECS cluster, created service to run 2 tasks, created a new load balancer with target group on port 8000(can be created seperately). Deployment failed with path issues to ECR. As tasks suppose to die and new tasks will come up. Hence, we can not use a static ip address for a task, rather we need to have a load balancer(which is static and basically a VM which is configured with nginx) and map the domain name with load balancer. Loadbalancer will automatically distribute the traffic to ecs tasks and will have target groups using ports(used by application) to connect to ecs tasks and we need to have a login page in the health checks so that while accessing our application, we should land on our login page or we can configure our application to have a endpoint as a health check. 

14. Load Balancer will go to Target group, which will connect to port 8000, target group is responsible to check and if found anything unhealthy, bring it down and if tasks is healthy, go to the login page. 

![alt text](Screenshots/image-67.png)

![alt text](Screenshots/image-68.png)

15. Hence, we have created a NAT Gateway, attach it to our route table for private subnet. Update the service with 0 tasks and rerun the deployment(if failed alerady). It will clear all tasks.

16. Updated the service with 2 tasks and rerun the deployment. Tasks were running but getting failed automatically. 

17. Created Security groups for ECS, Loadbalancer and RDS. Edited the ECS SG with a new inbound rule for port 8000 from LB-SG. Updated the Security group for Load Balancer, Database. Edited the inbound rule for RDS SG to listen from ECS SG on port 5432. 

![alt text](Screenshots/image-61.png)

18. Rerun the deployment with force. Tasks are running but keep getting failed. Checked the logs and it seems that as per step 14, target group is keep on finding the tasks as unhealthy and killing them. 

Complete Diagram:

![alt text](Screenshots/image-66.png)