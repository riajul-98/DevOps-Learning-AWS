# DevOps-Learning-AWS
Repository containing topics I learnt in the CoderCo AWS Module.

## AWS Global Infrastructure

### AWS Regions
Physical locations around the world where AWS has multiple data centres, ensuring resiliency and high availability. If one data centre fails, AWS quickly sqitches over to another data centre. They are the backbone of AWS. To choose a region, you would need to consider a few things such as latency, pricing, compliance and services. Each region has its own unique identifier. Most AWS services are region scoped. 

### AWS Availability Zones
Each region is made up of multiple AZs, usually 3. Separate from each other, isolated from disasters. Connected with high bandwidth, ultra-low latency networking. Each AZ is one or more discrete data centre with redundant power, networking and connectivity.

### Points of Presence (Edge Locations)
Roughly 400 points of presence. Content is delivered to end users with lower latency. Helpful for applications which require speed of delivery such as video streaming and gaming. 

## AWS IAM
IAM allows you to create users, groups and policies which lets you manage access to AWS resources. Groups makes it easier to grant permissions to a group of users with the same job role. Policies are used to define what a user or group can and cannot do on AWS. You can also allow temporary access through IAM roles. 

### IAM Users and Groups
- Root account: Created on AWS account creation. Has full access to everything on the account (very dangerous!). This should never be shared or used for day to day tasks. 
- Users: Individual people or services that need access to AWS. Each user gets their own login and are then given permissions. 
- Groups: A team of users which can be assigned permissions as a group. Does not contain other groups. Users do not have to belong to a group and users can belong to multiple groups. 

### IAM Permissions
Users and groups in AWS are assigned policies which are json documents which define what they are and aren't allowed to do. In AWS, always apply rhe least privilege principle (don't give more access than a user needs).

### IAM Policies Inheritance
How permissions are passed down to users and groups. Users inherit permissions which are assigned to their groups. E.g. if Charles is part of both the developer group and the DevOps group, he would inherit the permissions of both groups. Users can also have inline policies attached which are policies that are given to a user directly rather than through a group. 

### IAM Policies Structure
- Version: Policy language version, always include "2012-10-17".
- Id: An identifier for the policy (optional).
- Statement: One or more individual statements (required). This consists of:
    - Sid: An identifier for the statement (optional).
    - Effect: Where the statement allows or denies access (Allow, Deny).
    - Principal: Account/user/role to which this policy is applied to.
    - Action: List of actions this policy allows or denies.
    - Resource: List of resources to which the actions apply to.
    - Condition: Conditions for when this policy is in effect (optional).

An example can be seen below;

![alt text](image.png)

### IAM Password Policy
AWS allows you to set up a password policy to make sure every user is using strong, secure passwords. This includes a minimum password length, specific types of characters (uppercase letters, lowercase letters, numbers and non-alphanumeric characters), allow all IAM users to change their own passwords, enforce a password expiry policy and prevent password re-use. 

### Multi-Factor Authentications
Requires you to know the password as well as have the device which contains the MFA one-time code. Secures your account. RECOMMENDED!

MFA Devices includes:
- Virtual MFA: Application on your phone or device which generates a one time code (Google authenticator, Authy etc).
- Universal 2nd Factor (U2F) Security Key: A physical device you plug into your PC and authenticates your login without a code. Supports multiple users including the root user.
- Hardware Key Fob MFA Device: Physical devices which generate a new code every 30 seconds or so. 
- Hardware Key Fob MFA Device for AWS GovCloud (US): Specifically for AWS users in the GovCloud in the US. Similar to the above.

### User Access on AWS
- There are three options to access AWS:
    - AWS Management Console (protected by password and MFA)
    - AWS CLI (Protected by access keys)
    - AWS SDK (Protected by access keys)

- Access keys are generated through the AWS console.
- Users manage their own access keys.
- Access keys are secret, just like passwords. Don't share them!
- Access Key ID ~= username
- Secret Access Key ~= password

### AWS CLI
A super powerful tool that lets you interact with AWS services right from your command line. No need to login to your management console, every time you need to do something. You get direct access to the public API for AWS services. Great for developing scripts to manage resources. Open-source!

### AWS SDK
Super handy tool which lets you interact with AWS from your code. Language specific APIs. Lets you embed AWS services directly into your application. 

### IAM Roles for Services
Some AWS services will need to perform actions on your behalf. For this, we would need to grant them permissions using IAM roles. E.g. EC2 instance roles, Lambda Function roles, Roles for CloudFormation. This prevents us from hardcoding credentials into services. 

### IAM Security Tools
- IAM Credential Reports (Account level): A report that lists all your accounts users and the status of their various credentials.
- IAM Access Advisor (User level): Shows the service permissions granted to users and when those services were last accessed. Can be used to revise policies.

### IAM Guidelines and Best Practices
- Don't use the root account except when AWS account setup
- One physical user = One AWS user
- Assign users to groups and asign permissions to groups
- Create strong password policies
- Use and enforce the use of MFA
- Create and use Roles for giving permissions to AWS services
- Use access keys for programmatic access
- Audit permissions of your account using IAM Credential Reports and IAM Access Advisor
- Never share IAM USers and Access Keys

## AWS Compute

### AWS EC2
- Elastic Compute Cloud (Infrastructure as a Service).
- Renting servers on AWS.
- One of the most popular AWS services.
- It mainly consists in the capability of:
    - Renting VMs (EC2)
    - Storing data on virtual drives (EBS)
    - Distributing load across machines (ELB)
    - Scaling the services using an auto-scaling group (ASG)
- Only pay for what you use.

### EC2 Sizing and Configuration Options
- OS: Windows, Linux, MAC OS
- How much compute power and cores
- How much RAM
- How much storage space:
    - Network attached (EBS and EFS)
    - Hardware (Instance Store)
- Network card: Speed of the card, Public IP address
- Firewall rules: Security Groups
- Bootstrap script (configure at first launch): EC2 User Data

### EC2 User Data
EC2 user data allows us to run a script to bootstrap our instance (launching commands when a machine starts). It only runs once when at the time of instance first startup. It runs with the root user (full control of the system). Can be used to run tasks such as:
- Installing updates
- Installing software
- Downloading common files from the internet
- Anything you can think of

### EC2 Instance Types
- General purpose
- Compute Optimised
- Memory Optimised
- Storage Optimised
- Accelerated Computing
- HPC Optimised

### Security Groups
Like a virtual firewall. Fundamental for network security in AWS. Control how traffic is allowed in and out of our instances. Only contain allow rules. Security group rules can be referenced by IP addresses or by security groups. SGs are stateful (if you allow inbound traffic, the corresponding outbound traffic is allowed). They regulate:
- Access to ports
- Authorised IP ranges (IPv4 and IPv6)
- Control of inbound network 
- Control of outbound network

Can be attached to multiple instances. They are locked down to a region / VPC combination. Lives outside the EC2, therefore, if traffic is blocked, the EC2 instance won't see it. Its good to maintain a separate SG for SSH access! If your application is not accessible (time out), it is a SG issue. If your application gives a "connection refused" error, it is an application error or it has not been launched. All inbound traffic is blocked by default and all outbound traffic is authorised by default. 

Some ports we should know:
- 22: SSH
- 21: FTP
- 22: SFTP
- 80: HTTP
- 443: HTTPS
- 53: DNS
- 3389: RDP

### EC2 Instance Purchasing Options
- On-Demand Instance: Short workloads, predictable pricing, pay by second
- Reserved (1 & 3 Years):
    - Reserved Instances: Long workloads
    - Convertable Reserved Instances: Long workloads with flexible instances
- Saving Plans (1 & 3 Years): Commitment to an amount of usage, long workload
- Spot Instances: Short workload, cheap, can lose instances (less reliable)
- Dedicated Host: Book an entire physical server, control instance placement.
- Dedicated Instances: No other customer will share your hardware.
- Capacity Reservation: Reserve capacity in a specific AZ for any duration.

### Elastic IPs
Normally when you stop and restart an instance, the instance gets a new public IP. If we want a static IP, we can use elastic IPs. An Elastic IP is a public IPv4 IP you own as long as you don't delete it. You can attach it to one instance at a time. Can be remapped. When creating an EC2 instance which you want to allocate an Elastic IP, you should disable "Allocate public IP".

With an elastic IP, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account.. You can only have 5 Elastic IP in your account. Better to avoid using Elastic IPs as they reflect poor architecture decisions, instead use a random public IP and register a DNS name to it or use a Load Balancer and not a public IP.

### Elastic Block Storage (EBS) Volumes
A network drive that you can attach to your EC2 instances. Not a physical drive but works similarly. Allows your instances to persist data, even after termination. They are bound to a specific AZ.

### Amazon Machine Image (AMI) 
A customisation of an EC2 Instance:
- You add your own software, configuration, OS, monitoring...
- Faster boot / configuration time because the software comes pre-packaged.

Built for specific regions. You can launch EC2 instances from a public AMI (AWS provided), your own AMI (you make and maintain them yourself) or an AWS marketplace AMI (someone else made).

### Elastic File System (EFS)
Managed NFS (network file system) that can be mounted on many EC2s. Works with EC2 instances in multi-AZ. Highly available, scalable, expensive, pay per use.

### Elastic Load Balancer
Load balancers are servers that forward traffic to multiple servers (EC2 instances) downstream. Can also be known as a reverse proxy. If an EC2 instance goes down, the load balancer will know and send traffic to other healthy instances. Spreads the load and reduces instances from being overloaded. Exposes a single point of access (DNS) to your applications. Performs regular health checks on EC2 instances. Provides SSL termination for your websites. Enforces sticky sessions with cookies. High available across zones. Separates public and private traffic.

ELBs are managed load balancers which AWS guarantee will be working. AWS take care of upgrades, maintenance and high availability. Very easy to set up. Integrates with many AWS services such as EC2, ASG, ECS, ACM, CloudWatch, Route 53, WAF and Global Accelerator.

Health checks are crucial for load balancers. They enable the load balancer to know if instances are available to respond to requests. The health check is done on a port and a route (usually /health). If it responds with a non-200, then the instance is unhealthy.

### Types of Load Balancers on AWS
AWS has 4 kinds of managed Load Balancers:
- Classic Load Balancer: HTTP, HTTPS, TCP, SSL
- Application Load Balancer: HTTP, HTTPS, WebSocket
- Network Load Balancer: TCP, TLS, UDP
- Gateway Load Balancer: Operates at layer 3 (IP Protocols)

Some Load Balancers can be set up as internal (private) or external (public) ELBs

### Application Load Balancers
Layer 7 Load Balancer. Load balancing to multiple HTTP applications across machines (target groups) or across the same machine (containers). Support for HTTP/2 and websocket. Supports redirects (e.g. from HTTP to HTTPS). Smart enough and understands what is going on in the request such as headers, cookies and URLS.

Allow you to use routing tables to route to different target groups. Routing based on path in URL, hostnames, query strings and headers. Great fit for microservices and container-based applications. Has a port mapping feature to redirect to a dynamic port on ECS. 

Target groups are the resources which the load balancer will be routing traffic to. These can be:
- EC2 instances - HTTP
- ECS tasks - HTTP
- Lambda Functions - HTTP request is translated into a JSON event
- IP Addresses - Must be private IPs
- ALB can route to multiple target groups
- Health checks are at the target group level.

ALBs have fixed hostnames. The application servers don't see the IP of the client directly
- The true IP of the client is inserted in the header X-Forwarded-For
- We can also get the port (X-Forwarded-Port) and protocol (X-Forwarded-Proto)

### Network Load Balancers
Network Load Balancers (layer 4) allow to:
- Forward TCP and UDP traffic to your instances
- Handle millions of requests per second
- Less latency

Has one static IP per AZ and support assigning Elastic IP (helpful for whitelisting specific IP). Used for extreme performance, TCP or UDP traffic.

Great for applications which need to handle different protocol traffic.

### Sticky Sessions
Ensures the same client is always redirected to the same instance behind a load balancer. Works for classic, application and network load balancer. CLB and ALB use cookies which have expiration dates that you control. May bring imbalance to the load over the backend EC2 instances.

### Load Balancers and SSL certificates
Load balancers use X.509 certificate to make sure traffic between your users and instances are encrypted. You can manage the certificates using ACM. You can upload your own certificates alternatively. HTTP listeners:
- You must specify a default certificate
- You can add an optional list of certs to support multiple domains
- Clients can use SNI (Server Name Indication) to specify the host name they reach.
- Ability to specify a security policy to support older versions of SSL/TLS (legacy clients)

SNI solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites). Its a newer protocol and requires clients to indicate the hostname of the target server in the initial SSL handshake. The server will find the correct certificate or return the default one. Only works with ALB, NLB and CloudFront.

### Connection Draining
Also known as deregistration delay for ALB or NLB. Time to complete in-flight requests while the instance is de-registering or unhealthy. Stops sending new requests to the EC2 instance which is de-registering. Between 1 to 3600 seconds (default is 300 seconds). Can be disabled if set to 0 (cuts requests off instantly). Better to set a low value if your requests are short.

### Auto Scaling Group
When load in your application changes, an ASG is helpful in ensuring you do not have too many or not enough instances. The goal of an ASG is to:
- Scale out (add instances) if increased load
- Scale in (remove instances) if decreased load
- Ensure we have a minimum limit and maximum limit number of instances that run
- Automatically register new instances to the load balancer
- Re-create an EC2 instance in case a previous one is terminated

When creating an ASG, we set a minimum, a maximum and desired number of instances. The desired number is the number of instances your ASG will try to maintain this number of instances unless something changes.

You are able to use an AMI with an ASG to make sure new instances launch faster with the desired configurations.

- A Launch Template:
    - AMI + Instance Type
    - EC2 User Data
    - EBS Volumes
    - Security Groups
    - SSH Keypair
    - IAM Roles for your EC2 Instances
    - Network + Subnet Information
    - Load Balancer Information
- Min size / max size / initial capacity
- Scaling Policies

It is possible to scale an ASG based on CloudWatch alarms. An alarm monitors a metric (such as average CPU, memory, or a custom metric). Metrics such as average CPU are computed for the overall ASG instances. Based on the alarms, we can create scale out and scale in policies (increasing and decreasing the number of instances).

Dynamic Scaling Policies:
- Target Tracking Scaling: Simple to set up. Sets a target for a metric, e.g. I want the average ASG CPU to stay around 40%.
- Simple / Step Scaling: Based on CloudWatch alarms. When a alarm is triggered (e.g. CPU > 70%), then add 2 units. When an alarm is triggered (e.g. CPU < 30%), then remove 1.
- Scheduled Scaling: Anticipate a scaling based on known usage patterns. E.g. increase the min capacity to 10 on Friday at 5pm.

### Containers on AWS
ECR is Amazons own container repository. It is a private repository. They also have a public repository named ECR Public Gallery. 

AWS Container related services:
- Amazon ECS: Elastic Container Service. Amazons own container platform. 
- Amazon EKS: Elastic Kubernetes Service. Managd Kubernetes service (open source)
- AWS Fargate: Serverless container platform. Works with both ECS and EKS
- Amazon ECR: Container image repository.

#### ECS - EC2 Launch Type
- Launch Docker containers on AWS = Launch ECS Tasks on ECS Clasters
- EC2 Launch Type: You must provide and maintain the infrastructure (EC2 instances)
- Each EC2 instance must run the ECS agent to register in the ECS Cluster
- AWS takes care of starting / stopping containers

#### ECS - Fargate Launch Type
- You do not provision infrastructure (no EC2 instances to manage)
- Serverless
- You just create task definitions
- AWS just runs ECS tasks for you based on the CPU / RAM you need
- To scale, increase the number of tasks

#### ECS - IAM Roles
- EC2 Instance Profile (For the EC2 Launch Type only):
    - Used by the ECS agent
    - Makes API calls to ECS service
    - Sends container logs to CloudWatch logs
    - Pull Docker image from ECR
    - Reference sensitive data in Secrets Manager or SSM Parameter Store
- ECS Task Role:
    - Allows each task to have a specific role
    - Use different roles for the different ECS services you run
    - Defined in the task definition

#### ECS - Load Balancer Integrations
- ALB supported and works for most use cases.
- NLB recommended for only high throughput/high performance use cases or to pair with AWS Private Link
- CLB supported but not recommended. Lacks features.

#### ECS Service Auto Scaling
- Automatically increases/decreases the number of ECS tasks
- ECS Auto Scaling uses AWS Application Auto Scaling
    - ECS Service Average CPU Utilisation
    - ECS Service Average Memory Utilisation - Scale on RAM
    - ALB Request Count Per Target - Metric coming from ALB

- Target Tracking - Scale based on target value for a specific Cloudwatch metric
- Step Scaling - Scale based on a specific CloudWatch Alarm
- Scheduled Scaling - Scale based on a specific date/time (predictable changes)

- ECS Service Auto Scaling (task level) = EC2 Auto Scaling (EC2 Instance Level)
- Fargate auto scaling is much easier to set up

#### Amazon ECR
ECR is Amazons own container repository, similar to DockerHub. It is a private repository. They also have a public repository named ECR Public Gallery. Allows you to store, manage and pull docker images directly from AWS. Integrates directly with ECS, backed by S3. Access is controlled through IAM. Supports image vulnerability scanning, versioning, image tags, image lifecycle

#### Amazon EKS
- A way to launch managed Kubernetes clusters on AWS
- Alternative to ECS, similar goal but different API
- Supports EC2 if you want to deploy worker nodes or Fargate to deploy serverless containers
- Use case: If your company is already using Kubernetes on-premises or in another cloud and wants to migrate to AWS using Kubernetes.
- For multiple regions, deploy one EKS cluster per region
- Collect logs and metrics using CloudWatch Container Insights

Node Types:
- Managed Node Groups:
    - Creates and manages nodes (EC2 instances) for you
    - Nodes are part of an ASG managed by EKS
    - Supports on-demand or spot instances
- Self Managed Nodes:
    - Nodes created by you and registered to the EKS cluster and managed by an ASG
    - You can use pre-built AMI - Amazon EKS optimised AMI
    - Supports on-demand or spot instances
    - Better for more control and customisation
- AWS Fargate
    - No maintenance required; no nodes managed
    - Easiest to manage

### Serverless in AWS
- Serverless is a new paradigm where developers don't need to manage servers anymore, they just deploy code... they just deploy functions. Initially serverless was Function as a service. Pioneered by AWS Lambda but now includes anything that's managed; databases, messaging, storage, etc.
- Serverless does not mean there are no servers! It means you don't have to manage/provision/see them.

- AWS Lambda: Function as a service
- DynamoDB: NoSQL Database
- AWS Cognito: User authentication
- API Gateway: Allows you to create and monitor APIs which interact with backend services
- S3: Object storage
- SNS and SQS: Notification service and message queueing services (Messaging)
- Kinesis Data Firehose: Stream data
- Aurora serverless: Database which autoscales based on demand
- Step Functions: Manages and monitors workflows
- Fargate: Serverless container service

#### AWS Lambda
- Virtual functions - no servers to manage!
- Limited by time - short executions (15 mins max)
- Run on-demand (only when needed - pay for time used only)
- Scaling is automated!
- Benefits:
    - Easy pricing: Pay per request and compute time
    - Integrates with the whole AWS suite of services
    - Integrates with many programming languages
    - Easy monitoring with CloudWatch
    - Easy to get more resources per function (up to 10 GB)
    - Increasing RAM will also improve CPU and network!
- Supports:
    - Node.js (JavaScript)
    - Python
    - Go
    - Java
    - C#
    - Ruby
    - Custom Runtime API
    - Lambda Container Image: The container must implement the Lambda Runtime API. ECS/Fargate is preferred for running arbitrary Docker Images

## Amazon Networking

### Default VPC
- All new AWS accounts have a default VPC
- New Ec2 instances are launched into the default VPC if no subnet is specified
- Default VPC has internet connectivity and all EC2 instances inside it have public IPv4 addresses
- We also get a public and a private IPv4 DNS name

### Subnets (IPv4)
- AWS reserves 5 IP addresses (the first 4 and last 1) in each subnet
- These 5 addresses are not available for use and can't be assigned to an EC2 instance
- Example: if CIDR block 10.0.0.0/24, then reserved IP addresses are:
    - 10.0.0.0 - Network Address
    - 10.0.0.1 - Reserved by AWS for the VPC router
    - 10.0.0.2 - Reserved by AWS for mapping to Amazon-provided DNS (IP address of the DNS server)
    - 10.0.0.3 - Reserved by AWS for future use
    - 10.0.0.255 - Network broadcast address. AWS does not support broadcast in a VPC, therefore the address is reserved

### Internet Gateway (IGW)
- Allows resources in the VPC to connect to the internet
- Scales horizontally and is highly available and redundant
- Must be created separately from a VPC
- One VPC can only be attached to one IGW and vice versa
- IGWs on their own do not allow internet access
- Route tables must also be updated

### Bastion Hosts
- Used to SSH into our private EC2 instances
- Bastion is in a public subnet which then connected to all other private subnets
- Bastion Host SG must allow inbound traffic from the internet on port 22 from restricted CIDR, e.g. the public CIDR of your corporation
- SG of the EC2 instances must allow the SG of the bastion host or the private IP of the bastion host.

### NAT Gateway
- Allows instances in a private subnet to connect to the internet whilst blocking incoming traffic from the internet
- AWS managed NAT, higher bandwidth, high availability, no admin
- Pay per hour of usage and bandwidth
- NATGW is created in a specific AZ, uses an elastic IP
- Can't be used by EC2 instances in the same subnet (only from other subnets)
- Requires an IGW (Private subnet => NATGW => IGW)
- 5Gbps of bandwidth with automatic scaling up to 100Gbps
- No SG to manage/required
- If the AZ containing the NAT goes down, then all the resources in the other AZs will lose access to the internet. Solution for this is to have mutliple NAT gateways in multiple AZs. They are only highly available within an AZ.

### Network Access Control Lists (NACL)
- Like a firewall which control traffic from and to subnets
- One NACL per subnet, new subnets are assigned the default NACL
- You define NACL rules:
    - Rules have a number (1-32766), lower numbers have higher priority
    - First rule match will drive the decision
    - The last rule is an asterisk and denies a request in case of no rule match
    - AWS recommend adding rules by increment of 100
- Newly created NACLs deny everything
- Great way of blocking a specific IP address at the subnet level
- They are stateless

### VPC Peering
- A way to connect two VPCs together,, privately with AWS's internal network.
- The two VPCs behave as if they are part of the same network.
- Must not have overlapping CIDRs
- Not transitive (must be established for each VPC that need to communicate with one another)
- You must update route tables in each VPC's subnet to ensure EC2 instances can communicate with each other
- You can create VPC peering connections between VPCs in different accounts/regions
- You can reference a SG in a perred VPC (works cross account - same region)

### VPC Endpoints (AWS PrivateLink)
- Every AWS service is publicly exposed (public URL)
- VPC Endpoints (powered by AWS PrivateLink) allows you to connect to AWS services using a private network instead of using the public internet
- They're redundant and scale horizontally
- Remove the need for IGW, NATGW, ... to access AWS services
- In case of issues:
    - Check DNS setting resolution in your VPC
    - Check route tables
- Types of endpoints:
    - Interface Endpoints (powered by PrivateLink):
        - Provision an ENI (private IP address) as an entry point (must attach an SG)
        - Supports most AWS services
        - $ per hour + $ per GB of data processed
    - Gateway Endpoints:
        - Provisions a gateway and must be used as a target in a route table (does not need an SG)
        - Supports both S3 and DynamoDB
        - Free

### IPv6 in AWS
- IPv4 cannot be disabled for your VPC and subnets
- You can enable IPv6 (they're public IP addresses) to operate in dual-stack mode
- Your EC2 instances will get at least a private internal IPv4 and a public IPv6
- They can communicate using either IPv4 or IPv6 to the internet through an IGW

### Egress-only Internet Gateway
- Used for IPv6 only
- Similar to NATGW but for IPv6
- Allow instances in your VPC outbound connections over IPv6 whilst preventing the internet to initiate an IPv6 connection to your instances.
- You must update the route tables

## Amazon Route 53
- AWS managed DNS service. A highly available, scalable and fully managed Authoritative DNS (The customer can update the DNS records).
- Also a domain registrar
- Ability to check the health of your resources
- The only AWS service to provide 100% availability SLA

### Route 53 Hosted Zones
- A container for records that define how to route traffic to a domain and its subdomains
- Public Hosted Zones: Contain records that specify how to route traffic on the internet
- Private Hosted Zones: Contain records that specify how you route traffic within one or more VPCs

### Route 53 Records
- How you want to route traffic for a domain
- Each record contains:
    - Domain/subdomain name e.g. example.com
    - Record type e.g. A or AAAA
    - Value e.g. 12.34.56.78
    - Routing Policy - How Route 53 responds to queries
    - TTL - Amount of time the record cached at DNS resolver
- Route 53 supports the following DNS record types:
    - A/AAAA/CNAME/NS/CAA/DS/MX/NAPTR/PTR/SOA/TXT/SPF/SRV

### Route 53 Record Types
- A - Maps a hostname to IPv4
- AAAA - Maps a hostname to IPv6
- CNAME - Maps a hostname to another hostname
    - The target is a domain which must have an A or AAAA record
    - Can't create a CNAME record for the top node of a DNS namespace (Zone Apex)
- NS - Name Servers for the hosted zone
    - Control how traffic is routed for a domain

### Route 53 TTL
- High TTL e.g. 24 hours:
    - Less traffic on Route 53
    - Possibly outdated records
- Low TTL e.g. 60 seconds:
    - More traffic on route 53
    - Easy to change records
- Except for Alias records, TTL is mandatory for each DNS record.

### CNAME vs Alias
- AWS Resources expose an AWS hostname
- CNAME:
    - Point a hostname to any other hostname
    - Only for non root domains
- Alias:
    - Point to an AWS resource
    - Works for root and non root domains
    - Free of charge
    - Native health checks

### Alias Records
- Maps a hostname to an AWS resource
- Extension to DNS functionality
- Automatically recognises changes in the resources IP address
- Unlike CNAME, it can be used for the top node of a DNS namespace
- Always of type A/AAAA for AWS resouces
- Cannot set the TTL
- Targets include:
    - ELB
    - CloudFront Distributions
    - API Gateway
    - Elastic Beanstalk environments
    - S3 websites
    - VPC Interface Endpoints
    - Global accelerator
    - Route 53 records in the same hosted zone
    - Cannot set an Alias record for an EC2 DNS name

### Routing Policies:
    - Simple
    - Weighted
    - Failover
    - Latency Based
    - Grolocation
    - Multi-value answer
    - Geoproximity

#### Routing Policies - Simple
- Routes traffic to a single instance
- Can specify multiple values in the same record
- If multiple values are returned, a random one is chosen by the client
- When Alias is enabled, specify only one AWS resource
- Can't be associated with health checks

#### Routing Policies - Weighted
- Controls the percentage of the requests that go to each specific resource
- Assign each record a relative weight
    - Traffic = weight for a specific record / sum of all the weights for all records
    - Weights don't need to add up to 100
- DNS records must have the same name and type
- Can be associated with health checks
- Assign a weight of 0 to a record to stop sending traffic to a resource
- If all records have a weight of 0, then all records will be returned equally

#### Routing Policies - Latency Based
- Redirect the resource that has the least latency close to us
- Super helpful when latency for users is a priority
- Latency is based on traffic between users and AWS Regions
- Can be associated with health checks (has failover capability)

#### Route 53 Health Checks
- HTTP health checks are only for public resources
- Health checks => Automated DNS Failover
    1. Health checks that monitor endpoints
    2. Health checks that monitor other health checks
    3. Health checks that monitor cloudwatch alarms
- Integrated with CloudWatch metrics

#### Routing Policies - Geolocation
- Used to provide different content to users based on their location e.g. different languages, restricting content
- Routing users based on user location
- Specify location by continent, country, or state
- Should create a default record
- Can be associated with health checks

#### Routing Policies - Geoproximity
- Route traffic to your resources based on the geographic location of users and resources
- Ability to shift more traffic to resources based on a defined bias
- To change the size of the geographic region, specify bias values:
    - To expand (1 to 99) more traffic to the region
    - To shrink (-1 to -99) less traffic to the region
- Resources can be:
    - AWS resources
    - Non-AWS resources
- You must use Route 53 Traffic Flow to use this feature

#### Routing Policies - IP based Routing
- Routing is based on the clients IP address
- You provide a list of CIDRs for your clients and the corresponding endpoints/locations

#### Routing Policies - Multi-Value
When you have multiple resources and want route 53 to return more than one resource in response to a query, you use multi-value routing policy. Can be associated with health checks. Up to 8 healthy records are returned for each multi-value query. Not a substitute to having an ELB