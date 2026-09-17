# Security

## Overview

Security in this project is mainly based on network segmentation, controlled access, and keeping the database private.

The main security controls used in the environment are:

- VPC segmentation
- Security Groups
- Private RDS
- Transit Gateway routing
- HTTPS
- IAM role separation
- Keeping credentials out of the repository

---

## Network Segmentation

The environment is divided into three separate VPCs:

- Web VPC: `10.2.0.0/16`
- Testing VPC: `10.3.0.0/16`
- Database VPC: `10.5.0.0/16`

This keeps the Web, Testing, and Database environments separated.

Communication between the VPCs is handled through the Transit Gateway instead of using public routing.

---

## RDS Security

The RDS MySQL database is not publicly accessible.

The database uses:

```text
Port: 3306
Security Group: SG-RDS-Database
Public Access: Disabled

Inbound MySQL traffic is controlled through the RDS security group.

The Web environment was able to connect to the database through the private AWS network.

Security Groups

Dedicated Security Groups are used for the different resources in the environment.

Security Groups are used to control network traffic to:

Web EC2
Testing EC2
Database EC2
RDS MySQL

The RDS Security Group controls access to MySQL over TCP port 3306.

HTTPS

The website is served over HTTPS.

The configured hostname is:

https://ahmedinnovation.duckdns.org

A Let's Encrypt certificate is used for the DuckDNS hostname.

HTTPS was validated using:

curl -I https://ahmedinnovation.duckdns.org

The request returned:

HTTP/1.1 200 OK
IAM Access Control

IAM is used to separate access between different users and tasks.

The project includes:

Admin
Developer
Tester

The Developer and Tester roles have limited permissions based on their required tasks.

The Developer can manage the assigned EC2 instance and access the assigned S3 bucket.

The Tester has read-only EC2 permissions.

More details about these permissions are documented in:

docs/iam.md
Private Resources

The following resources are kept without public IP addresses:

Bastion Host
Testing EC2
Database EC2

The RDS instance also has public access disabled.

Private communication between the different environments is handled through the Transit Gateway.

Secrets

AWS credentials and other sensitive information are not included in the GitHub repository.

Credentials used during the lab should remain outside the documentation and source files.

The repository should not contain:

AWS Access Keys
AWS Secret Keys
Passwords
Private Keys
Database Credentials
Security Validation

The security configuration was checked during the lab by:

Verifying that RDS public access is disabled.
Testing private connectivity between the VPCs.
Checking that private EC2 instances have no public IP addresses.
Verifying HTTPS access to the website.
Checking the RDS Security Group configuration.
Reviewing the IAM roles and their permissions.
Keeping credentials and sensitive information out of the repository.
Security Summary

The project uses multiple layers of security rather than relying on a single control.

The main approach is:

VPC Segmentation
        |
Transit Gateway
        |
Security Groups
        |
Private Database
        |
IAM Access Control
        |
HTTPS

These controls were implemented as part of the AWS Secure Cloud Architecture lab.
