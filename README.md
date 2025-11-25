
# VPC Setup with Subnets and Routing

## Overview

This project builds a secure **Virtual Private Cloud (VPC)** on AWS with **public** and **private** subnets, correctly configured **route tables**, and an **Internet Gateway** (and optional NAT Gateway).  
It provides a reusable network foundation for hosting applications and databases.

---
**Components:**

- **VPC**: 10.0.0.0/16
- **Public subnet**: 10.0.1.0/24 (internet-facing)
- **Private subnet**: 10.0.2.0/24 (internal resources)
- **Internet Gateway**: outbound/inbound internet for public subnet
- **NAT Gateway** (optional): outbound internet for private subnet
- **Route Tables**: separate for public and private paths
- **Test EC2 instances**: one in each subnet

---

## Prerequisites

- Active AWS account
- Basic networking concepts (CIDR, subnets, routes)
- (Optional) SSH key pair and EC2 knowledge for testing

---

## Step-by-Step Setup

### 1. Create the VPC

1. Open **VPC** → **Your VPCs → Create VPC**.
2. Name: `project-vpc`.
3. IPv4 CIDR: `10.0.0.0/16`.
4. Create.

### 2. Create Public Subnet

1. Go to **Subnets → Create subnet**.
2. VPC: `project-vpc`.
3. Name: `public-subnet-1`.
4. Availability Zone: any in your region.
5. IPv4 CIDR: `10.0.1.0/24`.
6. Create.
7. Select subnet → **Edit subnet settings** → enable **Auto-assign public IPv4**.

### 3. Create Private Subnet

1. Again click **Create subnet**.
2. VPC: `project-vpc`.
3. Name: `private-subnet-1`.
4. IPv4 CIDR: `10.0.2.0/24`.
5. Create.

### 4. Internet Gateway

1. Go to **Internet Gateways → Create internet gateway**.
2. Name: `project-igw`.
3. Attach to `project-vpc`.

### 5. NAT Gateway (Optional but Recommended)

1. Allocate an **Elastic IP** in EC2.
2. Go to **NAT Gateways → Create NAT gateway**.
3. Subnet: `public-subnet-1`.
4. Elastic IP: choose the one you created.
5. Create and wait until `Available`.

### 6. Public Route Table

1. Go to **Route tables → Create route table**.
2. Name: `public-rt`.
3. VPC: `project-vpc`.
4. Create.
5. Select `public-rt` → **Routes → Edit routes**.
6. Add route:
   - Destination: `0.0.0.0/0`
   - Target: `Internet Gateway (project-igw)`.
7. **Subnet associations → Edit** → select `public-subnet-1`.

### 7. Private Route Table

1. Create another route table: `private-rt` for `project-vpc`.
2. Edit routes:
   - Destination: `0.0.0.0/0`
   - Target: `NAT Gateway` (if created).
3. Associate with `private-subnet-1`.

### 8. Launch Test EC2 Instances

- **Public instance**:
  - Subnet: `public-subnet-1`
  - Auto-assign public IP: enabled
  - Security group: allow SSH/HTTP from your IP/0.0.0.0/0
  - Test: ping/SSH / open web page.

- **Private instance**:
  - Subnet: `private-subnet-1`
  - No public IP
  - Security group: allow SSH from public instance SG (bastion approach)
  - Test: from public EC2, SSH into private EC2 and check outbound internet.

---

## Key Learnings

- How to design a VPC with public and private subnets.
- How to control routing using route tables, IGW, and NAT.
- How to isolate internal resources while still allowing outbound traffic.

Reference :https://dev.to/aws-builders/secure-aws-vpc-using-public-and-private-subnets-2ni3
