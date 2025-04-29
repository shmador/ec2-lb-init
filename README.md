## EC2 Maintenance Automation

This automation performs maintenance work on EC2 instances and manages their registration with a load balancer. Instances are removed from the target group during maintenance and re-added afterward.

The maintenance job is triggered remotely using EC2 user data.
