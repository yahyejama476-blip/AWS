# AWS
My progress on AWS

## VPC & Networking Assignment

### What I Built
Created a custom VPC from scratch with CIDR 10.0.0.0/16 in eu-west-2 (London). Configured one public subnet and one private subnet, an Internet Gateway for public access, a NAT Gateway in the public subnet so private resources can reach the internet, and two route tables to control traffic flow. Deployed two EC2 instances — one public facing and one private with no public IP — secured via security groups.

### VPC Details
Name: My-Custom-VPC
CIDR: 10.0.0.0/16
Region: eu-west-2 (London)
<img width="1916" height="742" alt="ALB 2" src="https://github.com/user-attachments/assets/375d147b-7bb0-480b-a3e3-5ecfbe2819f9" />


### Subnets
Public Subnet: 10.0.1.0/24
Private Subnet: 10.0.2.0/24
<img width="1917" height="885" alt="ALB 3" src="https://github.com/user-attachments/assets/ea03e3e7-9a2d-4ce6-9ba9-f9fddef66980" />


### Internet Gateway
Name: My-IGW
Attached to: My-Custom-VPC

### Route Tables
Public Route Table → 0.0.0.0/0 routes to My-IGW
<img width="1920" height="891" alt="ALB 4" src="https://github.com/user-attachments/assets/94890bc5-9276-4a4b-80b2-a6d224681896" />

Private Route Table → 0.0.0.0/0 routes to NAT Gateway

### NAT Gateway
Name: My-NAT-GW
Located in: Public Subnet

### EC2 Instances
Public Instance → Public Subnet, Public IP enabled, SSH and HTTP restricted to my IP only
Private Instance → Private Subnet, No Public IP, SSH accessible only from Public Instance Security Group

### User-Data Scripts
Used instance user-data to automatically install and configure a web server on launch without manual setup.

### What I Learnt
- VPC CIDR planning and how subnet size affects available IP addresses
- The difference between public route tables (via IGW) and private route tables (via NAT)
- How NAT Gateways let private instances access the internet without a public IP
- Security groups as stateful firewalls controlling who can reach each instance

### Challenges and How I Solved Them
- Could not reach private instance directly → learnt it has no public IP and must be accessed via the public instance as a bastion
- Could not route internet traffic to private subnet → fixed by adding default route via NAT Gateway to private route table
- Security group rules too open → tightened them so only my IP can SSH


---

## Load Balancer & Multi-AZ Assignment

### What I Built
Extended the VPC with a second public subnet across a different Availability Zone for fault tolerance. Deployed an Application Load Balancer spanning both zones, registered two EC2 instances in a Target Group, and configured security groups so the ALB is open to the internet but EC2 is only reachable from within the VPC.


### Architecture
- VPC: 10.0.0.0/16
- Public-Subnet-A: 10.0.1.0/24 — eu-west-2a
- Public-Subnet-B: 10.0.3.0/24 — eu-west-2b
- Application Load Balancer: Internet-facing, spanning 2 Availability Zones
- Target Group: HTTP protocol, port 80, health check path `/`
- 2 EC2 instances registered — Status: Healthy
  <img width="1920" height="878" alt="ALB 5" src="https://github.com/user-attachments/assets/c2900938-8283-4f29-a06d-fd737e1cc499" />

### Security Groups
- ALB Security Group: Allow HTTP 80 from 0.0.0.0/0 — open to all internet traffic
- EC2 Web Security Group: Allow HTTP 80 from 10.0.0.0/16 — restricted to VPC only
  <img width="1920" height="807" alt="ALB 6" src="https://github.com/user-attachments/assets/c4553e96-91f1-4c63-9283-dae29332fb28" />
<img width="1920" height="800" alt="ALB 8" src="https://github.com/user-attachments/assets/89b0dcd2-e79e-4a84-ac96-dd3989263aac" />


### What I Learnt
- How Load Balancers distribute traffic across multiple instances for high availability
- Deploying across 2 Availability Zones so the service survives a data centre outage
- How to use security groups as layered firewalls — ALB public, EC2 private
- Health checks automatically detect unhealthy instances and stop sending traffic to them

### Challenges and How I Solved Them
- ALB timing out when accessed → fixed by creating a dedicated EC2 security group allowing traffic from the VPC CIDR instead of attaching the wrong security group to EC2
- Target Group showing zero instances → resolved by manually registering both EC2 instances
- Confused about which security group goes where → learnt ALB and EC2 need separate security groups with different rules
