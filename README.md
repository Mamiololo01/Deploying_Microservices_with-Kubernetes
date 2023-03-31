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

Make sure to select your own key-pair or create your own, enable a public-ip address, and configure your subnet and VPC correctly. Addtionally, MicroK8s requires at least 20Gbs of disc space, I pushed mine to the three-tier limit of 30 just to be safe.

<img width="1156" alt="Screenshot 2023-03-31 at 17 16 06" src="https://user-images.githubusercontent.com/67044030/229200355-78222b18-22b7-4f6d-a9ae-88f81d09582b.png">

<img width="1184" alt="Screenshot 2023-03-31 at 17 16 19" src="https://user-images.githubusercontent.com/67044030/229200582-8c863d0d-f511-464a-b574-da826eba698b.png">


Additionally, you will need to configure your security groups for the following ports. Our goal is to make our cluster work first. We can always tighten it up before deploying to production later.

<img width="959" alt="Screenshot 2023-03-31 at 17 26 47" src="https://user-images.githubusercontent.com/67044030/229200781-c3f76477-f283-4e51-b770-e3f8970d7949.png">

Once our instance is created, we need to connect to it, begin updating our packages and install MicroK8s:

<img width="993" alt="Screenshot 2023-03-31 at 17 27 29" src="https://user-images.githubusercontent.com/67044030/229200934-911f92ba-4f7e-4813-bda9-125f5ae597e5.png">


Once in our shell, we need to update our packages and install microK8s. The “sudo snap install microk8s — classic” command does the following:

sudo — runs the command with administrative privileges
snap — snap packages are self-contained software that includes all dependencies
install — the command used to install a snap package
microk8s — the name of the snap package being installed
— classic — an option that allows the package to access system resources and perform privilege operations, which makes it possible to install and run MicroK8s on the host operating system

<img width="727" alt="Screenshot 2023-03-31 at 17 36 46" src="https://user-images.githubusercontent.com/67044030/229201238-3f0ba7c1-110c-42ff-af6c-b227c9d6bdab.png">

<img width="710" alt="Screenshot 2023-03-31 at 17 37 15" src="https://user-images.githubusercontent.com/67044030/229201468-56ecce09-529c-4509-aca7-34209e29a988.png">

<img width="716" alt="Screenshot 2023-03-31 at 17 38 08" src="https://user-images.githubusercontent.com/67044030/229201794-f320a3eb-fa7a-4faa-ba0d-be97c944e05c.png">

<img width="710" alt="Screenshot 2023-03-31 at 17 39 52" src="https://user-images.githubusercontent.com/67044030/229202213-4616380a-cb8f-4303-bf96-f9739e6599bc.png">

Next, we will change our permissions to allow user “ubuntu” to run administrative MicroK8 commands without having to use the ‘sudo’ command

In order for the these effects to take place, we must exit and re-enter our shell by logging out and back into our instance via SSH.

Step 2 — Setting up our IDE on our Instance for MicroK8s
Once we log back in, we next need to continue our configuration of our Kubernetes environment

First, let’s check to see if we have a valid configuration file. By default, MicroK8s does not install with one. We can check if this is our case by running the “kubectl config view” command:

<img width="579" alt="Screenshot 2023-03-31 at 17 41 48" src="https://user-images.githubusercontent.com/67044030/229203367-b5ac237d-6719-4bb4-bfb1-d40b92a84e4a.png">

When I installed microK8s on my instance, I had no configuration file so I had to create one using the following commands:

mkdir -p $HOME/.kube

sudo cp -i /var/snap/microk8s/current/credentials/client.config $HOME/.kube/config #Creates a new configure file

sudo chown $(id -u):$(id -g) $HOME/.kube/config

These commands create the $HOME/.kube directory if it does not already exist, copy the MicroK8s configuration file to the $HOME/.kube/config file, and change the owner of the $HOME/.kube/config file to the current user. This allows you to use the kubectl command-line tool to interact with the MicroK8s cluster.

The configuration file is needed for your cluster to properly connect to the API server. Without this file and the following configurations, when you try to deploy your cluster, the shell will return the following error:

“Error: The connection to the server localhost:8080 was refused-did you specify the right host or port?”

Once your ‘config’ file is created and in the correct directory, you can continue your configuration of your k8 environment

Set your cluster name and connect to the correct URL for the Kubernetes API. Depending on when you are reading this article, the URL may have changed:

kubectl config set-cluster my_web_server --server=https://127.0.0.1:16443 --insecure-skip-tls-verify=true

Next, set a username and password. I used the user name ‘ubuntu’ and password of ‘123456789’.

kubectl config set-credentials ubuntu --token=123456789

Next, name our context. I choose ‘first_context’. Addtionally, you need to set the cluster name and username that you used in the previous steps.

kubectl config set-context first_context --cluster=my_web_server --user=ubuntu

Next, we set our current working context to our context name from our previous steps. The context determines which cluster, user, and namespace are used for subsequent ‘kubectl’ commands which we will soon use to create our first cluster.

kubectl config use-context first_context

Step 3 — Deploying our Kubernetes Cluster

Finally, we get to the fun part! Actually launching our cluster. In order to launch a cluster, Kubernetes needs a valid YAML file configured to specs.

In a previous article, I launched a docker stack using a docker compose file. I have converted that docker YAML into a Kubernetes YAML which will launch the following service pods:

10 Apache Web Servers
1 Postgres Database
1 Postgres Replica Database
4 Redis Caching Databases
Here is the docker compose YAML:


You will need to create your own YAML file into your instance file system using VIM or Nano.

Once your YAML file is created and saved in your instance, run the following command exchanging my filename for the one you chose:

sudo kubectl apply -f mydeployment.yaml
Lastly to validate that your cluster is running, you can run the following command:


