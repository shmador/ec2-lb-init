## EC2 Load-Balancer Initialization

This automation runs maintenance tasks on EC2 instances during initialization and registers them with a load balancer.

The job is triggered via EC2 user data that calls a Jenkins job API. Instances are initialized and registered as part of the setup process.
