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