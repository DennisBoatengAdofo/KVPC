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
*![KCVPCCreation 2](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/5dca275a-5ab2-40bf-9ba1-51402ec4891d)


**2. CREATE SUBNETS**
* **_Public Subnet_**
* Go to the Subnets section.
* Click on Create Subnet.
* Fill in the details:
* Name: PublicSubnet
* VPC: KCVPC
* Availability Zone: eu-west-1a (or any one from your region)
* IPv4 CIDR block: 10.0.1.0/24
* Click on Create.

* ![PublicSubnet](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/8401a773-72de-4082-b474-609a068ef2d7)


* **_Private Subnet_**
* Go to the Subnets section.
* Click on Create Subnet.
* Fill in the details:
* Name: PrivateSubnet
* VPC: KCVPC
* Availability Zone: eu-west-1a 
* IPv4 CIDR block: 10.0.2.0/24
* Click on Create.

* ![PrivateSubnet](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/efbaf4a6-34f8-4fa0-a716-ba8faae325fa)


**3. CONFIGURE INTERNET GATEWAY**
* Go to the Internet Gateways section.
* Click on Create internet gateway.
* Name it KCVPC-IGW.
* Click on Create.
* Select the created IGW and click on Attach to VPC.
* Select KCVPC and attach.

* ![Internet Gateway Created   Attached](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/ccb50e38-f38a-4d83-af74-aff0ecc2508e)


**4. CONFIGURE ROUTE TABLES**
* **Public Route Table**
* Go to the Route Tables section.
* Click on Create route table.
* Fill in the details:
* Name: PublicRouteTable
* VPC: KCVPC
* Click on Create.
* Select the created route table and go to the Routes tab.
* Click on Edit routes and add a route:
* Destination: 0.0.0.0/0
* Target: Internet Gateway (KCVPC-IGW)
* Click on Save routes.
* Go to the Subnet associations tab.
* Click on Edit subnet associations and associate PublicSubnet.

* ![PublicRouteTable](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/24ceb41f-08b3-422e-968b-b5189c6ba88a)


* **Private Route Table**
* Go to the Route Tables section.
* Click on Create route table.
* Fill in the details:
* Name: PrivateRouteTable
* VPC: KCVPC
* Click on Create.
* Go to the Subnet associations tab.
* Click on Edit subnet associations and associate PrivateSubnet.

* ![PrivateRouteTable](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/3866ae2b-e2f4-4cde-8086-7f9f8273af06)


**5.CONFIGURE NAT GATEWAY**
* Go to the NAT Gateways section.
* Click on Create NAT Gateway.
* Fill in the details:
* Subnet: PublicSubnet
* Elastic IP allocation: Click on Allocate Elastic IP and select it.
* Click on Create NAT Gateway.
* Go back to the PrivateRouteTable's Routes tab.
* Click on Edit routes and add a route:
* Destination: 0.0.0.0/0
* Target: NAT Gateway (Select the created NAT Gateway)

* ![NAT Gateway Configuration](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/52eb17aa-8283-4b21-b700-d2c54ddc419d)


  

**6.SET UP SECURITY GROUPS**
_*Public Security Group_
* Go to the Security Groups section.
* Click on Create security group.
* Fill in the details:
* Name: PublicSG
* Description: Security group for public instances
* VPC: KCVPC
* Click on Create security group.
* Select the created security group and go to the Inbound rules tab.
* Click on Edit inbound rules and add the following rules:
* Type: HTTP, Protocol: TCP, Port Range: 80, Source: 0.0.0.0/0
* Type: HTTPS, Protocol: TCP, Port Range: 443, Source: 0.0.0.0/0
* Type: SSH, Protocol: TCP, Port Range: 22, Source: Your local IP
* Click on Save rules.
  
*![PublicSecurityGroup](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/cbb802c7-a9dc-47a2-b088-c4944a324f48)


_**Private Security Group**_
* Go to the Security Groups section.
* Click on Create security group.
* Fill in the details:
* Name: PrivateSG
* Description: Security group for private instances
* VPC: KCVPC
* Click on Create security group.
* Select the created security group and go to the Inbound rules tab.
* Click on Edit inbound rules and add the following rule:
* Type: MySQL/Aurora, Protocol: TCP, Port Range: 3306, Source: PublicSG
* Click on Save rules.

* ![PrivateSecurityGroup](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/fce0f296-bbc4-41fb-8fab-acb37bb092ef)


**7. CONFIGURE NETWORK ACLs**
_**Public Subnet NACL**_
* Go to the Network ACLs section.
* Click on Create network ACL.
* Fill in the details:
* Name: PublicNACL
* VPC: KCVPC
* Click on Create.
* Select the created NACL and go to the Inbound rules tab.
* Click on Edit inbound rules and add the following rules:
* Rule #: 100, Type: HTTP, Protocol: TCP, Port Range: 80, Source: 0.0.0.0/0, Allow
* Rule #: 110, Type: HTTPS, Protocol: TCP, Port Range: 443, Source: 0.0.0.0/0, Allow
* Rule #: 120, Type: SSH, Protocol: TCP, Port Range: 22, Source: 0.0.0.0/0, Allow
* Go to the Outbound rules tab and click on Edit outbound rules.
* Add the following rule:
* Rule #: 100, Type: All Traffic, Protocol: All, Port Range: All, Source: 0.0.0.0/0, Allow

* ![PublicNACL](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/90ee8cb0-09b8-4f18-827b-8dc962d20451)


_**Private Subnet NACL**_
* Go to the Network ACLs section.
* Click on Create network ACL.
* Fill in the details:
* Name: PrivateNACL
* VPC: KCVPC
* Click on Create.
* Select the created NACL and go to the Inbound rules tab.
* Click on Edit inbound rules and add the following rule:
* Rule #: 100, Type: All Traffic, Protocol: All, Port Range: All, Source: PublicSubnet CIDR, Allow
* Go to the Outbound rules tab and click on Edit outbound rules.
* Add the following rules:
* Rule #: 100, Type: All Traffic, Protocol: All, Port Range: All, Source: 0.0.0.0/0, Allow

* ![PublicNACL](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/ea0e9837-77dc-4a53-a10b-ede204c85dc8)


**8. DEPLOY INSTANCES**
*_ Public Instance_
* Go to the EC2 Dashboard.
* Click on Launch Instance.
* Fill in the details:
* Name: PublicInstance
* AMI: Select an appropriate AMI (e.g., Amazon Linux 2)
* Instance Type: t2.micro (or any type you prefer)
* Key Pair: Select or create a key pair
* Network Settings: Select KCVPC and PublicSubnet
* Security Group: Select PublicSG
* Click on Launch.

* ![Both Public   Private EC2 Instance](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/8f98ac45-7fb1-44b8-b295-65c35e275ca2)


_* Private Instance_
* Go to the EC2 Dashboard.
* Click on Launch Instance.
* Fill in the details:
* Name: PrivateInstance
* AMI: Select an appropriate AMI (e.g., Amazon Linux 2)
* Instance Type: t2.micro (or any type you prefer)
* Key Pair: Select or create a key pair
* Network Settings: Select KCVPC and PrivateSubnet
* Security Group: Select PrivateSG
* Click on Launch.
* ![Both Public   Private EC2 Instance](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/cba02ca1-1ed1-458e-bc1d-cbe5ed4957d2)


**9. VPC Architecture Diagram**
 *![VPC Architecture Diagram](https://github.com/DennisBoatengAdofo/KVPC/assets/173356424/a528f2d7-695b-4c5d-b27e-02afffc75555)



**10. COMPONENTS EXPLANATIONS**
* VPC (Virtual Private Cloud): A logically isolated network within the AWS cloud where you can launch AWS resources.
* Subnets
* _Public Subnet_: A subnet that has access to the internet through an Internet Gateway.
* _Private Subnet_: A subnet that doesn't have direct access to the internet.
**Internet Gateway (IGW)*: A gateway that allows instances in the public subnet to connect to the internet.
***Route Tables**
* _Public Route Table_: Manages routing for the public subnet, including a route to the IGW for internet access.
* _Private Route Table_: Manages routing for the private subnet without direct internet access.
* **NAT Gateway*: Allows instances in the private subnet to connect to the internet while preventing inbound traffic from the internet.
* **Security Groups**: Virtual firewalls for instances to control inbound and outbound traffic.
* Network ACLs
* Stateless, subnet-level firewalls to control inbound and outbound traffic at the subnet level.













