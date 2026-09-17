# AWS Secure Cloud Architecture

A secure and segmented AWS cloud architecture project for a PHP-based web application.

This project demonstrates practical implementation of AWS networking, IAM, security controls, monitoring, alerting, and validation testing.

---

## Architecture Overview

The environment is divided into three separate VPCs:

* **Web VPC** — `10.2.0.0/16`
* **Testing VPC** — `10.3.0.0/16`
* **Database VPC** — `10.5.0.0/16`

The three VPCs are connected through an **AWS Transit Gateway (TGW-01)** for private inter-VPC communication.

The web application runs on **Amazon EC2** using Apache and PHP, while the database is hosted on **Amazon RDS for MySQL** with public access disabled.

---

## AWS Services

* Amazon VPC
* Internet Gateway
* NAT Gateway
* AWS Transit Gateway
* Amazon EC2
* Amazon RDS for MySQL
* AWS IAM
* Security Groups
* Amazon CloudWatch
* Amazon SNS

---

## Security

The architecture includes:

* Network segmentation between Web, Testing, and Database environments
* Private RDS database
* Controlled MySQL access on TCP `3306`
* Security Groups
* IAM role-based access control
* Separation of Admin, Developer, and Tester permissions
* HTTPS using a Let's Encrypt certificate
* No credentials or secret values stored in the repository

---

## Monitoring & Alerting

Amazon CloudWatch monitors the Web EC2 instance and RDS database.

Alerts are delivered through Amazon SNS.

### EC2 High CPU

```text
CPUUtilization > 70%
Period: 5 minutes
Statistic: Average
```

The alarm was successfully tested using controlled CPU load.

Observed CPU utilization:

```text
≈ 99.67%
```

The alarm transitioned to `ALARM` and generated an SNS email notification.

### RDS High Connections

```text
DatabaseConnections > 50
Period: 5 minutes
Statistic: Average
```

During testing, CloudWatch recorded approximately:

```text
60.25 connections
```

The alarm transitioned to `ALARM` and generated an SNS email notification.

---

## Validation

The architecture was validated through practical tests including:

* EC2 high-CPU monitoring test
* RDS high-connections monitoring test
* Transit Gateway connectivity testing
* HTTPS validation
* Web-to-RDS connectivity validation

The RDS connection test returned to a normal connection count after the additional sessions were terminated.

---

## Web Application

The application is hosted on Amazon EC2 using:

```text
OS: Amazon Linux 2023
Web Server: Apache 2.4.68
PHP: 8.5.10
Protocol: HTTPS
Certificate: Let's Encrypt
```

Public hostname:

```text
ahmedinnovation.duckdns.org
```

---

## Database

The database tier uses Amazon RDS for MySQL.

```text
DB Identifier: admin-rds-mysql
Engine: MySQL Community
Port: 3306
Public Access: Disabled
Database: website_db
Table: visitors
```

Connectivity from the Web environment to the RDS endpoint was successfully validated.

---

## IAM

The project uses role-based access control with three permission levels:

| Role      | Access                            |
| --------- | --------------------------------- |
| Admin     | Full AWS access                   |
| Developer | Controlled EC2 and S3 permissions |
| Tester    | Read-only EC2 observation         |

Detailed IAM policies are available in the `policies/` directory.

---

## Repository Structure

```text
aws-secure-cloud-architecture/
│
├── README.md
│
├── docs/
│   ├── architecture.md
│   ├── network.md
│   ├── iam.md
│   ├── security.md
│   ├── monitoring.md
│   └── testing.md
│
├── diagrams/
│   ├── architecture.drawio
│   ├── architecture.png
│   └── architecture.pdf
│
├── screenshots/
│   ├── iam/
│   ├── networking/
│   ├── rds/
│   └── monitoring/
│
└── policies/
    ├── developer-policy.json
    └── tester-policy.json
```

---

##  Project Status

**Status:** Training Lab / Portfolio Project

**AWS Region:** `us-east-1`

The documentation represents the active training environment and its validated components.

---

## Security Note

Do not commit:

* AWS access keys
* Passwords
* Private keys
* Database credentials
* API keys
* Other sensitive information

Sensitive values should remain outside the GitHub repository.
