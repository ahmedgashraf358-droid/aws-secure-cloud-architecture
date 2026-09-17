# Architecture

## Overview

The project is built using three separate AWS VPCs:

- Web VPC: `10.2.0.0/16`
- Testing VPC: `10.3.0.0/16`
- Database VPC: `10.5.0.0/16`

AWS Transit Gateway (`TGW-01`) is used to provide private connectivity between the three VPCs.

The public website is hosted on an EC2 instance in the Web VPC.  
The database is hosted on Amazon RDS for MySQL in the Database VPC with public access disabled.

---

## Web VPC

**CIDR:** `10.2.0.0/16`

The Web VPC contains the main web application and the Bastion Host.

### Website EC2

The Website EC2 is located in the public subnet.

- Subnet: `10.2.100.0/24`
- Availability Zone: `us-east-1b`
- Private IP: `10.2.100.186`
- Public IP: `100.49.169.65`
- Operating System: Amazon Linux 2023
- Apache: `2.4.68`
- PHP: `8.5.10`

The instance hosts the PHP web application and is the public entry point for the website.

HTTPS is enabled using a Let's Encrypt certificate.

### Bastion Host

The Bastion Host is located in a private subnet.

- Subnet: `10.2.128.0/20`
- Private IP: `10.2.134.137`
- Public IP: None

The Bastion Host is used for private access and connectivity testing inside the AWS environment.

---

## Testing VPC

**CIDR:** `10.3.0.0/16`

The Testing VPC is separated from the Web and Database VPCs and is mainly used for testing network connectivity.

### Testing EC2

The Testing EC2 is located in the private subnet.

- Subnet: `10.3.128.0/20`
- Private IP: `10.3.131.22`
- Public IP: None

Connectivity to this instance was tested using its private IP through the Transit Gateway.

---

## Database VPC

**CIDR:** `10.5.0.0/16`

The Database VPC contains the database infrastructure.

### Database EC2

The Database EC2 is located in a private subnet.

- Subnet: `10.5.128.0/20`
- Private IP: `10.5.138.195`
- Public IP: None

This instance is used for database-side connectivity validation.

### RDS MySQL

The application database is hosted on Amazon RDS for MySQL.

- Identifier: `admin-rds-mysql`
- Engine: MySQL Community
- Port: `3306`
- Public access: Disabled
- Security Group: `SG-RDS-Database`
- Database: `website_db`
- Table: `visitors`

The RDS instance is not directly accessible from the Internet.

The Web EC2 was able to connect to the RDS instance through the private AWS network using TCP port `3306`.

---

## Subnets

### Web VPC

The Web VPC contains the following subnets:

**Public Subnet**
- CIDR: `10.2.0.0/20`
- Availability Zone: `us-east-1a`
- No listed workload

**Private Subnet**
- CIDR: `10.2.128.0/20`
- Availability Zone: `us-east-1a`
- Workload: Bastion Host

**Website Public Subnet**
- CIDR: `10.2.100.0/24`
- Availability Zone: `us-east-1b`
- Workload: Website EC2

### Testing VPC

The Testing VPC contains:

**Public Subnet**
- CIDR: `10.3.0.0/20`
- Availability Zone: `us-east-1a`
- No listed workload

**Private Subnet**
- CIDR: `10.3.128.0/20`
- Availability Zone: `us-east-1a`
- Workload: Testing EC2

### Database VPC

The Database VPC contains:

**Public Subnet**
- CIDR: `10.5.0.0/20`
- Availability Zone: `us-east-1a`
- No listed application workload

**Private Subnet**
- CIDR: `10.5.128.0/20`
- Availability Zone: `us-east-1a`
- Workload: Database EC2

**Private Subnet 2**
- CIDR: `10.5.150.0/24`
- Availability Zone: `us-east-1b`
- Used as an RDS subnet-group member

---

## Transit Gateway

**Name:** `TGW-01`

**ID:** `tgw-000b060df9c1c1e1a`

The Transit Gateway is the central private connectivity point between the three VPCs.

The active propagated routes are:

- `10.2.0.0/16` → Web VPC
- `10.3.0.0/16` → Testing VPC
- `10.5.0.0/16` → Database VPC

The old `10.4.0.0/16` route is not part of the current architecture.

---

## Routing

Private traffic between the VPCs is routed through the Transit Gateway.

### Web Private Subnet

The main routes are:

```text
0.0.0.0/0     -> NAT Gateway
10.3.0.0/16   -> Transit Gateway
10.5.0.0/16   -> Transit Gateway
Testing Private Subnet

The main routes are:

0.0.0.0/0     -> NAT Gateway
10.2.0.0/16   -> Transit Gateway
10.5.0.0/16   -> Transit Gateway
Database Private Subnet

The main routes are:

0.0.0.0/0     -> NAT Gateway
10.2.0.0/16   -> Transit Gateway
10.3.0.0/16   -> Transit Gateway

Internet-bound traffic from private subnets follows the configured NAT path, while traffic between the VPCs uses the Transit Gateway.

Application Access

The website is available through:

https://ahmedinnovation.duckdns.org

HTTPS was tested using:

curl -I https://ahmedinnovation.duckdns.org

The response returned:

HTTP/1.1 200 OK

The domain resolves to the public IP of the Website EC2.

Database Connectivity

The Web EC2 was used to test connectivity to the RDS MySQL database.

The connection uses:

Protocol: TCP
Port: 3306
Database: website_db

The visitors table was found during the validation process and contained two records at the time of testing.

Connectivity Testing

Private connectivity through the Transit Gateway was tested between the following instances:

10.2.134.137 -> 10.3.131.22
10.2.134.137 -> 10.5.138.195

These tests were used to verify connectivity between the Web, Testing, and Database VPCs.

Architecture Decisions

The main architecture decisions are:

Three separate VPCs are used to keep the environments segmented.
Transit Gateway is used as the central private connectivity hub.
The Website EC2 is placed in a public subnet because it is the public entry point of the application.
The Bastion Host is located in a private subnet.
Testing resources are separated into a dedicated VPC.
RDS public access is disabled.
Database traffic uses the private network and TCP port 3306.
CloudWatch and SNS are used for monitoring and alert notifications.
The old 10.4.0.0/16 route is excluded from the current architecture.

The final network diagram should be checked against the AWS Console before publishing it as the final version.
