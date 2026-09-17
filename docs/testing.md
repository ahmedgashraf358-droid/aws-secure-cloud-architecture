# Testing

## Overview

The AWS environment was tested to verify the main security, network, application, and monitoring configurations.

The tests focused on:

- EC2 CPU monitoring
- RDS database connections monitoring
- HTTPS access
- Transit Gateway connectivity

---

## EC2 High CPU Test

The Website EC2 was tested using a controlled CPU load with `stress-ng`.

The CPU utilization reached approximately:

```text
99.67%

The configured CloudWatch alarm threshold was:

CPUUtilization > 70%

The alarm entered the ALARM state after the CPU utilization exceeded the configured threshold.

An SNS email notification was also received.

Test flow:

stress-ng
    |
High CPU Usage
    |
CloudWatch
    |
EC2 High CPU Alarm
    |
SNS
    |
Email Notification
RDS High Connections Test

The RDS MySQL database was tested by opening controlled MySQL sessions from the Web EC2.

The observed values were:

Threads_connected: 63
CloudWatch DatabaseConnections: approximately 60.25

The configured alarm threshold was:

DatabaseConnections > 50

The RDS High Connections alarm entered the ALARM state.

An SNS email notification was received.

After the test, the additional database sessions were terminated.

The connection count returned to:

3
HTTPS Validation

The public website was tested using curl.

Command:

curl -I https://ahmedinnovation.duckdns.org

The response included:

HTTP/1.1 200 OK

This confirmed that the website was reachable over HTTPS during the validation test.

Transit Gateway Connectivity Test

Private connectivity between the VPCs was tested through the Transit Gateway.

The following private IP addresses were used during the validation:

Web VPC:
10.2.134.137

Testing VPC:
10.3.131.22

Database VPC:
10.5.138.195

Connectivity was tested between the Web environment and the Testing and Database environments through the Transit Gateway.

The active Transit Gateway routes included:

10.2.0.0/16
10.3.0.0/16
10.5.0.0/16
Test Results
EC2 High CPU
Result: Passed
CPU: approximately 99.67%
Threshold: > 70%
Alarm: ALARM
SNS Notification: Received
RDS High Connections
Result: Passed
Threads_connected: 63
CloudWatch DatabaseConnections: approximately 60.25
Threshold: > 50
Alarm: ALARM
SNS Notification: Received
HTTPS
Result: Passed
Response: HTTP/1.1 200 OK
Transit Gateway
Result: Passed
Web -> Testing
Web -> Database
Additional Notes

The RDS High CPU alarm was configured but no separate failure test was recorded.
The RDS Low Storage alarm was also configured but was not force-tested.
These alarms were therefore treated as configured monitoring controls rather than tested failure scenarios.

Summary

The main application, network, and monitoring paths were validated.
The EC2 High CPU and RDS High Connections alarms were tested successfully and generated SNS email notifications.
HTTPS access and private connectivity through the Transit Gateway were also validated.
