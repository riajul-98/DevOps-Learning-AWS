# DevOps-Learning-AWS
Repository containing topics I learnt in the CoderCo AWS Module.

## AWS Global Infrastructure

### AWS Regions
Physical locations around the world where AWS has multiple data centres, ensuring resiliency and high availability. If one data centre fails, AWS quickly sqitches over to another data centre. They are the backbone of AWS. To choose a region, you would need to consider a few things such as latency, pricing, compliance and services. Each region has its own unique identifier. Most AWS services are region scoped. 

### AWS Availability Zones
Each region is made up of multiple AZs, usually 3. Separate from each other, isolated from disasters. Connected with high bandwidth, ultra-low latency networking. Each AZ is one or more discrete data centre with redundant power, networking and connectivity.

### Points of Presence (Edge Locations)
Roughly 400 points of presence. Content is delivered to end users with lower latency. Helpful for applications which require speed of delivery such as video streaming and gaming. 

