# Network

## Overview

The AWS environment is divided into three VPCs.

- Web VPC: `10.2.0.0/16`
- Testing VPC: `10.3.0.0/16`
- Database VPC: `10.5.0.0/16`

The three VPCs use AWS Transit Gateway (`TGW-01`) for private communication.

The Web VPC is used for the website and private access.  
The Testing VPC is used for connectivity validation.  
The Database VPC contains the private database tier.

DNS resolution and hostnames are enabled in all three VPCs.

---

## Web VPC

**CIDR:** `10.2.0.0/16`

The Web VPC contains the public website and the private Bastion Host.

### Public Subnet

- Availability Zone: `us-east-1a`
- CIDR: `10.2.0.0/20`
- Type: Public
- No listed workload

### Private Subnet

- Availability Zone: `us-east-1a`
- CIDR: `10.2.128.0/20`
- Type: Private
- Workload: Bastion Host

### Website Public Subnet

- Availability Zone: `us-east-1b`
- CIDR: `10.2.100.0/24`
- Type: Public
- Workload: Website EC2

The Website EC2 uses the public subnet because it is the public entry point of the application.

---

## Testing VPC

**CIDR:** `10.3.0.0/16`

The Testing VPC is separated from the other environments and is used mainly for network and Transit Gateway testing.

### Public Subnet

- Availability Zone: `us-east-1a`
- CIDR: `10.3.0.0/20`
- Type: Public
- No listed workload

### Private Subnet

- Availability Zone: `us-east-1a`
- CIDR: `10.3.128.0/20`
- Type: Private
- Workload: Testing EC2

The Testing EC2 does not have a public IP and is accessed through private connectivity.

---

## Database VPC

**CIDR:** `10.5.0.0/16`

The Database VPC is used for the private database environment.

### Public Subnet

- Availability Zone: `us-east-1a`
- CIDR: `10.5.0.0/20`
- Type: Public
- No listed application workload

### Private Subnet

- Availability Zone: `us-east-1a`
- CIDR: `10.5.128.0/20`
- Type: Private
- Workload: Database EC2

### Private Subnet 2

- Availability Zone: `us-east-1b`
- CIDR: `10.5.150.0/24`
- Type: Private
- Used as an RDS subnet-group member

---

## Transit Gateway

The three VPCs are connected through AWS Transit Gateway.

**Name:** `TGW-01`

**ID:** `tgw-000b060df9c1c1e1a`

The Transit Gateway acts as the central routing point for private traffic between the VPCs.

The active VPC routes are:

```text
10.2.0.0/16 -> Web VPC
10.3.0.0/16 -> Testing VPC
10.5.0.0/16 -> Database VPC

The current configuration does not include the old 10.4.0.0/16 route.

Route Tables

The private subnets use the Transit Gateway for traffic going to the other VPCs.

Web Private Routing
0.0.0.0/0   -> NAT Gateway
10.3.0.0/16 -> Transit Gateway
10.5.0.0/16 -> Transit Gateway

The default route is used for Internet-bound traffic, while the Testing and Database VPC traffic is sent through the Transit Gateway.

Testing Private Routing
0.0.0.0/0   -> NAT Gateway
10.2.0.0/16 -> Transit Gateway
10.5.0.0/16 -> Transit Gateway

Traffic to the Web and Database VPCs uses the Transit Gateway.

Database Private Routing
0.0.0.0/0   -> NAT Gateway
10.2.0.0/16 -> Transit Gateway
10.3.0.0/16 -> Transit Gateway

Traffic to the Web and Testing VPCs uses the Transit Gateway.

Network Connectivity

Private connectivity was tested between the Web-side private host and the test instances in the other VPCs.

Web to Testing
10.2.134.137 -> 10.3.131.22
Web to Database
10.2.134.137 -> 10.5.138.195

These tests were used to verify that the Transit Gateway routes were working between the VPCs.

Database Network Access

The RDS MySQL instance is located in the Database VPC.

It is not publicly accessible.

The database listens on:

TCP 3306

Access to MySQL is controlled through the RDS security group:

SG-RDS-Database

The Web EC2 was able to connect to the RDS endpoint through the private AWS network.

Network Security

The network is separated into different VPCs instead of placing all resources in a single network.

The main network security points are:

Web, Testing, and Database workloads are separated.
Inter-VPC traffic uses the Transit Gateway.
RDS public access is disabled.
Database traffic uses TCP port 3306.
Security Groups control access to the resources.
Private instances do not use public IP addresses.
Internet-bound traffic from private subnets uses the configured NAT path.
Network Validation

The following checks were completed during the lab:

Verified the three VPC CIDR ranges.
Verified the private subnets and their workloads.
Verified the Transit Gateway routes.
Tested private connectivity between the Web, Testing, and Database environments.
Verified that the RDS instance is not publicly accessible.
Verified connectivity to RDS through TCP port 3306.
Confirmed that the old 10.4.0.0/16 route is not part of the current configuration.
Current Network Structure

The network can be summarized as:

                    Internet
                       |
                Internet Gateway
                       |
                  Web VPC
                10.2.0.0/16
                       |
                Transit Gateway
                    TGW-01
                 /           \
                /             \
       Testing VPC          Database VPC
      10.3.0.0/16          10.5.0.0/16
            |                    |
       Testing EC2          Database EC2
                              |
                           RDS MySQL
                            TCP 3306

The Transit Gateway provides the private connection between the three VPCs, while the Website EC2 provides the public entry point for the web application.
