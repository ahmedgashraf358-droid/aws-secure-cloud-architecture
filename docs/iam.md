# IAM

## Overview

IAM was used to separate the access levels in the AWS environment.

The project uses three main roles:

- Admin
- Developer
- Tester

The Developer and Tester permissions are limited to the actions needed for their tasks.

---

## Admin

The Admin role has full access to AWS resources.

The policy uses:

```text
Action: *
Resource: *

This role is intended for administrative tasks and full control of the AWS environment.

Developer

The Developer role has permissions related to EC2 management and access to the assigned S3 bucket.

EC2 Permissions

The Developer can:

Start the assigned EC2 instance
Stop the assigned EC2 instance
Reboot the assigned EC2 instance
Terminate the assigned EC2 instance
View EC2 instances
View EC2 instance status
View EC2 tags

The EC2 actions include:

ec2:StartInstances
ec2:StopInstances
ec2:RebootInstances
ec2:TerminateInstances
ec2:DescribeInstances
ec2:DescribeInstanceStatus
ec2:DescribeTags
S3 Permissions

The Developer can work with the assigned S3 bucket.

The permissions include:

s3:ListBucket
s3:GetObject
s3:PutObject
s3:DeleteObject

The Developer role does not have full AWS access.

Tester

The Tester role is used for read-only EC2 observation.

The Tester can:

View EC2 instances
View EC2 instance status
View EC2 tags

The permissions are:

ec2:DescribeInstances
ec2:DescribeInstanceStatus
ec2:DescribeTags

The Tester role does not include permissions to start, stop, reboot, or terminate EC2 instances.

Role Separation

The roles are separated based on the type of access required.

Admin

Full AWS access for administrative tasks.

Developer

EC2 management for the assigned instance and access to the assigned S3 bucket.

Tester

Read-only access for observing EC2 resources.

This separation helps demonstrate role-based access control and the principle of least privilege for the Developer and Tester roles.

Security Notes

No AWS credentials or sensitive information are included in this repository.

IAM policies should only provide the permissions required for the intended task.

The actual policy files used for the project can be stored under:

policies/

with the following structure:

policies/
├── developer-policy.json
└── tester-policy.json
