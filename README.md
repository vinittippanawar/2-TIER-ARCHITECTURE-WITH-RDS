# 🏗️ AWS 2-Tier Architecture with RDS

- Hands-on project demonstrating deployment of a 2-Tier Architecture on AWS, consisting of a Web Tier (Public Subnet) and a Database Tier (Private Subnet).
The setup ensures secure communication, data persistence, and isolation of the database layer using Amazon RDS and EC2 instances.

# 🧩 Architecture Overview

- Architecture Type: 2-Tier
- Components:

- Frontend Tier: EC2 instance in Public Subnet (Web Server)

- Backend Tier: Amazon RDS (MariaDB/MySQL) in Private Subnets

- Communication: Web EC2 ↔ RDS over port 3306 (secured via Security Groups)
