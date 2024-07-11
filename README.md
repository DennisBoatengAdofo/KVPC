# KCVPC Setup Guide
This guide details the steps to design and set up a Virtual Private Cloud (VPC) in AWS EU-West-1 (Ireland) region with both public and private subnets. The setup will include routing, security groups, and network access control lists (NACLs) to ensure proper communication and security within the VPC.


### Table of Contents ###
1. Create VPC
2. Create Subnets
3. Configure Internet Gateway
4. Configure Route Tables
5. Configure NAT Gateway
6. Set Up Security Groups
7. Configure Network ACLs
8. Deploy Instances
9. VPC Architecture Diagram
10. Component Explanations

**1. CREATE VPC**
* Go to the VPC Dashboard.
* Click on Create VPC.
* Fill in the details:
* Name: KCVPC
* IPv4 CIDR block: 10.0.0.0/16
* Click on Create.

