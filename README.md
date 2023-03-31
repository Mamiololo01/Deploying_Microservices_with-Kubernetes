# Deploying_Microservices_with-Kubernetes
Kubernetes Deployments for Micro-service Applications

The project of building a Kubernetes cluster with multiple worker pods of different types can be very useful for businesses that need to run various micro-services as part of their software infrastructure.

For instance, maybe you manage an e-commerce company that needs to handle a large volume of web traffic, process transactions and manage its customer database. In such a scenario, you can use Kubernetes to deploy worker pods that handle each of these services separately.

Deploying pods using Redis can handle session management and caching while deploying pods using Apache can handle web serving and load balancing to prevent your front end from both overloading and over-scaling.

Meanwhile, Postgres pods can handle your company’s customer database, ensuring that it is always available and secure.

By using Kubernetes to deploy and manage these pods, you can ensure that each service runs independently of the others, with no interference or downtime.

Additionally, the company can scale each service separately based on demand, ensuring that resources are used efficiently and cost-effectively.

Overall, the use of Kubernetes for managing micro-services can provide businesses with greater flexibility, scalability, and reliability in their software infrastructure.

If you’re new to Kubernetes and looking for a way to get started, then this tutorial is for you. I will walk you thru step-by-step everything you need to deploy your first cluster

Pre-Requisites
Basic knowledge of Kubernetes including concepts such as Pods, Services, Deployments, and Replication Controllers
Knowledge of Docker containers including docker images
Familiarity with AWS such as EC2, VPCs, subnetting, & security groups
Experience with Infrastructure as Code (IaC) tools.
Knowledge of Linux and CLI commands

Step 1 —Launching our EC2 Instance

We start by launching our EC2 instance via the console. I will be installing and utilizing commands for Ubuntu Linux. I will be installing MicroK8s which is a lightweight and fast Kubernetes distribution designed for developers.

Note that MicroK8s requires minimum system requirements above AWS’s free-tier eligible instances:

Ubuntu 16.04 LTS or later (or a compatible Linux distribution)
At least 2 CPU cores
At least 2GB of RAM
At least 20GB of disk space

For this reason, I will be deploying a t3.medium instance so that the installation and orcastration runs smoothly and as expected. When I tried running on a smaller instance the installation consistently failed.


