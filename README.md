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

For private subnet, no IGW route was added.

Created a Route Table named my-rt and associated private subnet to it.

✅ Now my architecture had proper separation — web in public, DB in private.

# 🖥️ 2. LAUNCH EC2 INSTANCES

-🔹 Public EC2 – Web Server

- OS: Amazon Linux 2023

- Subnet: public-subnet

- Security Group: launch-wizard-9

- Inbound rules:

- HTTP (80) → Anywhere

- SSH (22) → My IP

- Key Pair: webkey.pem

-🔹 Private EC2 – Database Server (initially used before RDS)

- Subnet: private-subnet

- Security Group: launch-wizard-10

- Inbound: My web server’s private IP

- Key Pair: dbkey.pem

✅ Uploaded dbkey.pem from local machine to web server using SCP:

```bash
scp -i "C:\Users\vinit\Downloads\webkey.pem" "C:\Users\vinit\Downloads\dbkey.pem" ec2-user@<web-public-ip>:/home/ec2-user/

```
✅ Then, I SSH’d from web server → private DB server:

```bash
ssh -i dbkey.pem ec2-user@<private-ip>

```
# 🌐 3. CONFIGURED WEB SERVER
- Installed and configured LEMP Stack (Linux, Nginx, MariaDB, PHP):
  
   ```bash
  sudo dnf install nginx php mariadb105-server php-mysqlnd -y
  sudo systemctl start nginx
  sudo systemctl enable nginx
  sudo systemctl start php-fpm
  sudo systemctl enable php-fpm
  ```
- Then I went to /usr/share/nginx/html/ and created web files:
  
``` bash
sudo nano form.html
sudo nano submit.php
```
# 💾 4. CONFIGURED DATABASE (RDS)
🔹 Created a DB Subnet Group (important step)

- While creating RDS, I created a DB Subnet Group named db-subnet-group.
- It included both private subnets (for high availability across two AZs):

- 10.0.2.0/24 → Availability Zone: us-east-1a

- 10.0.3.0/24 → Availability Zone: us-east-1b

✅ This was required because RDS needs minimum 2 subnets in different AZs to ensure redundancy.

🔹 RDS Configuration:

- Engine: MariaDB

- Deployment type: Single DB instance

- DB Name: studentdb

- Username: admin

- Password: Pass$123

- VPC: vpc-2tier

- Subnet group: db-subnet-group (created manually)

- Security Group: launch-wizard-10 (private DB SG)

- Availability Zones: 2 AZs (auto-selected from subnet group)

- Public Access: No

✅ After creation, I copied the RDS Endpoint:



