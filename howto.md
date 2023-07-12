
# How to Set Up a Jump Box to Access Isolated Database in AWS

In this guide, we will walk you through the process of setting up an AWS project that involves using an EC2 instance as a "jump box" to securely access an isolated database. The jump box acts as an intermediary server, providing controlled and secure access to the database.

## Table of Contents

- [Step 1: Create a Virtual Private Cloud (VPC)](#step-1-create-a-virtual-private-cloud-vpc)
- [Step 2: Create Subnets](#step-2-create-subnets)
- [Step 3: Set Up Internet Gateway and Route Tables](#step-3-set-up-internet-gateway-and-route-tables)
- [Step 4: Launch an EC2 Instance as the Jump Box](#step-4-launch-an-ec2-instance-as-the-jump-box)
- [Step 5: Set Up Security Groups](#step-5-set-up-security-groups)
- [Step 6: Set Up the Isolated Database (Amazon RDS)](#step-6-set-up-the-isolated-database-amazon-rds)
- [Step 7: Establish SSH Tunnel and Access the Database](#step-7-establish-ssh-tunnel-and-access-the-database)
- [Conclusion](#conclusion)

## Step 1: Create a Virtual Private Cloud (VPC)

1. Go to the AWS Management Console and navigate to the VPC service.
2. Click on "Create VPC" to start creating a new VPC.
3. Provide a name for your VPC and enter the desired IPv4 CIDR block (e.g., `10.0.0.0/16`).
4. Configure the VPC's advanced options, such as DNS resolution and DNS hostnames, according to your requirements.
5. Click on "Create VPC" to create the VPC.
...

## Step 2: Create Subnets

1. Within the VPC dashboard, click on "Subnets" and then "Create subnet."
2. Provide a name for the subnet and select the VPC you created in the previous step.
3. Choose an appropriate IPv4 CIDR block for the subnet (e.g., `10.0.1.0/24`).
4. Select the availability zone in which you want to create the subnet.
5. Repeat these steps to create both a public and private subnets. (Make sure you have 1 private subnet in atleast 2 different zones, otherwise you will not be able to create your database.

## Step 3: Set Up Internet Gateway and Route Tables

1. In the VPC dashboard, click on "Internet Gateways" and then "Create internet gateway."
2. Provide a name for the internet gateway and click on "Create internet gateway."
3. Select the newly created internet gateway and click on "Attach to VPC."
4. Choose the VPC you created in Step 1 and click on "Attach internet gateway."

## Step 4: Launch an EC2 Instance as the Jump Box

1. In the EC2 dashboard, click on "Launch Instance" to start the instance creation process.
2. Choose an Amazon Machine Image (AMI) that matches your preferred operating system.
3. Select an instance type based on your requirements.
4. Configure the instance details:
   - Select the VPC you created in Step 1.
   - Choose the public subnet you created in Step 2.
   - Enable "Auto-assign Public IP" to ensure the instance receives a public IP.
   - Configure additional settings as per your requirements.
5. Add storage, tags, and security groups as needed.
6. Review the instance details and click on "Launch."
7. Select or create a key pair for SSH access and proceed with launching the instance.

## Step 5: Set Up Security Groups

1. In the EC2 dashboard, click on "Security Groups" and then "Create security group."
2. Provide a name for the security group and select the VPC you created in Step 1.
3. Configure inbound rules to allow SSH access from your IP address (e.g., source: `0.0.0.0/0`, port: `22`).
4. Review the settings and click on "Create security group."

## Step 6: Set Up the Isolated Database (Amazon RDS)

1. In the Amazon RDS dashboard, click on "Subnet groups" and then "Create DB subnet group."
2. Provide a name and description for the subnet group, this security group should have a rule permiting traffic from the tunnel instance with the correct port for your database, for mysql the port is 3306.

![image](https://github.com/mjcvideira/aws-project-sshtunnel/assets/114146806/075c904c-3023-4efe-aa00-b27816421093)


3. Select the private subnets you created in Step 2 and click on "Create."
4. Click on "Databases" and then "Create database" to create a new database.
5. Choose the desired database engine (e.g., Amazon Aurora, MySQL) and version.
6. Configure the database settings, such as DB instance identifier, username, and password.
7. Select the VPC you created in Step 1 and the subnet group you created in Step 6.
8. Configure additional settings, such as storage, backups, and monitoring, based on your requirements.
9. Review the settings and click on "Create database" to create the isolated database.

![image](https://github.com/mjcvideira/aws-project-sshtunnel/assets/114146806/c9d44a43-aa24-4bbf-a81e-8555126c40d9)

You will need this dns name under Endpoint & Port



## Step 7: Establish SSH Tunnel and Access the Database

Open a terminal or command prompt on your local machine.
Use the following command to establish an SSH tunnel to the jump box instance:

 ssh -i path/to/key.pem -L <local-port>:<database-endpoint>:<database-port> ec2-user@<jump-box-public-ip>

Replace path/to/key.pem with the path to your private key file.
Replace <local-port> with the desired local port for port forwarding (e.g., 3306 for MySQL).
Replace <database-endpoint> and <database-port> with the endpoint and port of the isolated database.
Replace <jump-box-public-ip> with the public IP of the jump box instance.

This is mine : ssh -v -i chavemestra.pem -f -N -L 3306:database-1-tunel-instance-1.cclvulz7vygk.us-east-1.rds.amazonaws.com:3306 ec2-user@18.215.11.194

This should be the output, if the tunnel was successfully created

![image](https://github.com/mjcvideira/aws-project-sshtunnel/assets/114146806/fad16520-5cc0-4554-9428-b2d9cbe760dd)
Note: you should be on the dir where you have the .pem key

Once the SSH tunnel is established, you can use database management tools (e.g., MySQL, pgAdmin) to connect to localhost:<local-port> on your local machine and interact with the isolated database.
