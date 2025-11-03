# 🏗️ AWS 2-Tier Architecture with RDS

- Hands-on project demonstrating deployment of a 2-Tier Architecture on AWS, consisting of a Web Tier (Public Subnet) and a Database Tier (Private Subnet).
The setup ensures secure communication, data persistence, and isolation of the database layer using Amazon RDS and EC2 instances.

# 🧩 Architecture Overview

- Architecture Type: 2-Tier
- Components:

- Frontend Tier: EC2 instance in Public Subnet (Web Server)

- Backend Tier: Amazon RDS (MariaDB/MySQL) in Private Subnets

- Communication: Web EC2 ↔ RDS over port 3306 (secured via Security Groups)

             Internet
               │
         ┌────────────┐
         │  IGW + RT  │
         └────────────┘
               │
       ┌──────────────────────┐
       │ Public Subnet (Web)  │
       │  EC2 (Nginx + PHP)   │
       │  SG: web-sg          │
       └──────────────────────┘
               │
      (TCP 3306 allowed)
               │
       ┌──────────────────────┐
       │ Private Subnet (DB)  │
       │   Amazon RDS (MySQL) │
       │   SG: rds-sg         │
       └──────────────────────┘

# ⚙️ 1. VPC SETUP 

 - I created a manual VPC named vpc-2tier with CIDR 10.0.0.0/16.
 - 🔹 Steps I performed:

 -Created 2 Subnets:

 -public-subnet → 10.0.1.0/24 (for Web Server)

 -private-subnet → 10.0.2.0/24 (for Database/RDS)
 
-Attached an Internet Gateway (IGW) and edited route table for public subnet with:
 -Destination: 0.0.0.0/0  
 -Target: igw-id
