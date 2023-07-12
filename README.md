# aws-project-sshtunnel
A simple Secure Access Project: Utilizing an EC2 Instance as a "Jump Box" to Access a Isolated Database
# Project Jump Box

This is a simple AWS project that involves using an EC2 instance as a "jump box" to securely access an isolated database. The jump box acts as an intermediary server, providing controlled access to the database and enhancing its security.

## Problem

The objective of this project is to establish a secure and cost-effective method to access a database located in a private subnet within our VPC. By using a jump box in a public subnet, we can avoid the need for a NAT gateway or exposing the database to a public subnet.
![image](https://github.com/mjcvideira/aws-project-sshtunnel/assets/114146806/d738c361-938b-47ab-821e-45c939a53148)

## Solution

The solution involves the following steps:

1. Set up a new VPC specifically for this project, including the necessary subnets and routes.
2. Create an EC2 instance that will serve as the jump box in the public subnet.
3. Configure the security group for the jump box to allow SSH access.
4. Create the isolated database in the private subnet, ensuring it is not directly accessible from the internet.
5. Configure the security group for the database to allow access only from the jump box and specific IP addresses if required.
6. Establish an SSH tunnel between your local machine and the jump box to securely access the database.
7. Use appropriate database management tools to interact with the database through the SSH tunnel.

## Benefits

- Enhanced Security: By using a jump box, direct access to the isolated database is restricted, reducing the attack surface.
- Cost-effective: This method is more affordable compared to using a NAT gateway or a public subnet.
- Centralized Access Control: Access to the database is controlled through the jump box, providing centralized monitoring and management.
