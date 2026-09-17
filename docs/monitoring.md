# Monitoring

## Overview

AWS CloudWatch is used to monitor the EC2 web server and the RDS database.

Amazon SNS is used to send email notifications when an alarm enters the ALARM state.

The SNS topic used in the project is:

```text
CloudInfrastructure-Alerts
EC2 Monitoring

An alarm was created for the Website EC2 CPU utilization.

High CPU Alarm
Alarm: EC2-Website-HighCPU
Metric: CPUUtilization
Condition: > 70%
Period: 5 minutes
Statistic: Average

This alarm was tested using a controlled CPU load on the Website EC2.

The CPU utilization reached approximately:

99.67%

The alarm entered the ALARM state and an SNS email notification was received.

The test flow was:

CPU Utilization
      |
  CloudWatch
      |
   Alarm
      |
     SNS
      |
    Email
RDS Monitoring

Three CloudWatch alarms were configured for the RDS MySQL instance.

High CPU
Alarm: RDS-admin-mysql-HighCPU
Metric: CPUUtilization
Condition: > 70%
Period: 5 minutes
Statistic: Average

This alarm was configured but no separate failure test was recorded.

Low Storage
Alarm: RDS-admin-mysql-LowStorage
Metric: FreeStorageSpace
Condition: < 2 GiB
Threshold: 2147483648 bytes
Period: 5 minutes
Statistic: Average

The threshold direction was corrected during the configuration.

This alarm was not force-tested.

High Connections
Alarm: RDS-admin-mysql-HighConnections
Metric: DatabaseConnections
Condition: > 50
Period: 5 minutes
Statistic: Average

This alarm was tested using controlled MySQL sessions from the Web EC2.

The CloudWatch DatabaseConnections value reached approximately:

60.25

The alarm threshold was:

50

The alarm entered the ALARM state and an SNS email notification was received.

SNS Notification

All EC2 and RDS alarms publish their ALARM state notifications to:

CloudInfrastructure-Alerts

The notification path is:

EC2 / RDS Metric
       |
CloudWatch Alarm
       |
      SNS
       |
Email Notification
Monitoring Tests
EC2 High CPU Test

A controlled CPU load was generated on the Website EC2 using stress-ng.

The observed CPU utilization reached approximately:

99.67%

This exceeded the configured threshold of:

70%

The alarm changed from OK to ALARM and the SNS notification was received.

RDS High Connections Test

Controlled MySQL sessions were opened from the Web EC2 to the RDS endpoint.

The observed values were:

Threads_connected: 63
CloudWatch DatabaseConnections: approximately 60.25

The configured alarm threshold was:

DatabaseConnections > 50

The alarm entered the ALARM state and the SNS notification was received.

After the test, the additional database sessions were terminated.

The connection count returned to:

3
Alarm Status

The current monitoring setup can be summarized as follows:

EC2 High CPU
> 70%
Tested
ALARM + SNS Email

RDS High CPU
> 70%
Configured
Not separately tested

RDS Low Storage
< 2 GiB
Configured
Not force-tested

RDS High Connections
> 50
Tested
ALARM + SNS Email
Monitoring Summary

CloudWatch provides monitoring for the main compute and database resources.

SNS provides the notification path for alarm events.

The EC2 High CPU and RDS High Connections alarms were successfully tested during the lab, while the RDS High CPU and Low Storage alarms were configured without a separate failure test.
