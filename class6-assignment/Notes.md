Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service from Amazon Web Services that simplifies running Kubernetes, an open-source container orchestration system, on the AWS cloud and on-premises. EKS automates the management of the Kubernetes control plane – the core system that schedules containers, stores cluster data, and manages availability – allowing users to focus on deploying, scaling, and managing their containerized applications.

Amazon ECS (Elastic Container Service) is a fully managed container orchestration service by Amazon Web Services (AWS) that automates the deployment, management, and scaling of containerized applications, allowing users to run Docker containers without managing the underlying infrastructure. It offers a serverless option (AWS Fargate) for running containers on Amazon-managed infrastructure, as well as the option to run on a cluster of your own Amazon EC2 virtual machines for more control

** To simulate, the load testing scenario, we have 5 containers running as per diagram in docker compose & docker compose is only for testing and simulation on a VM. However, in real scenario, for AWS the best idea would be to use either ECS or EKS. 

ECS:
  Pros: Easy to use and no need to spend hours on management.
  Cons: Limitation on scalability and flexibility

EKS:
   Pros: Allows to do anything or any customizations    
   Cons: Additional resources required for management


** Email Alerting can be done by using AWS SES services. 

Diagram:

![alt text](Screenshots/image-0.png)

For the start:

ECS allows you to manage containers on a logical level and is not a phyical running resource like EKS. Hence, it prevents the cost and billing. 

A ECS task would be a simple template which contains, image(you use), port, network, container app, env, cpu/memory), which start, complete the job a& stop. It can be completed in 2 forms. Json is preferable during terrform.

![alt text](Screenshots/image-1.png)


If in ECS, we want some containers to be up & keep running. We have ECS services, where we can define number of tasks running and if we tell ECS to run all those containers and in case one of container is killed, ECS services will make sure that all containers should be running and bring another container as a part of autoscaling and runtime. This is also a task definition running inside the ECS services. Containers running under a ECS service, cna talk to each other as they are running on a localhost.

Between 2 or more ECS services, a mapping is required for communication. 

A individual tasks can have a public or private I.P Address. In production environment, public ip address is not used. 
Anything which is serverless, it is actually running on a VM in backend and the difference we do not need to manage the VM & it's managed by Provider. Hence, it's server less for a client.

Three kind of VMs

a. EC2 - A particular machine and image. Create autoscaling group and use it
b. Fargate - Server less. 
c. Own Datacenter machines due to security & compliance and for custom needs. 

![alt text](image-3.png)






