# VPC & Networking Assignment

## VPC
- VPC Name: My-Custom-VPC
- CIDR: 10.0.0.0/16
- Region: eu-west-2 (London)

## Subnets
- Public-Subnet: 10.0.1.0/24
- Private-Subnet: 10.0.2.0/24

## Internet Gateway
- Name: My-IGW — attached to My-Custom-VPC

## Route Tables
- Public-RT: 0.0.0.0/0 → My-IGW
- Private-RT: 0.0.0.0/0 → NAT Gateway

## NAT Gateway
- Name: My-NAT-GW — located in Public-Subnet

## EC2 Instances
- Public-Instance: Public-Subnet, Public IP assigned, SSH/HTTP from My IP only
- Private-Instance: Private-Subnet, NO Public IP, SSH only from Public EC2 Security Group
