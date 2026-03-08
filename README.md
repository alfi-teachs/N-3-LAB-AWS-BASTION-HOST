# N3-LAB---AWS-BASTION-HOST

# Step 1: Create VPC

Login to AWS Console.

Go to VPC Dashboard.

Click Create VPC.

Select VPC only.

Name tag: My-VPC.

IPv4 CIDR block: 10.0.0.0/16.

Click Create VPC.

# Step 2: Create Public Subnet

Go to Subnets.

Click Create Subnet.

Select My-VPC.

Subnet name: Public-Subnet.

Availability zone: any (example ap-south-1a).

IPv4 CIDR block: 10.0.1.0/24.

Click Create Subnet.

# Step 3: Create Private Subnet

Click Create Subnet again.

Select My-VPC.

Subnet name: Private-Subnet.

Availability zone: same zone.

IPv4 CIDR block: 10.0.2.0/24.

Click Create Subnet.

# Step 4: Enable Auto Public IP (Public Subnet)

Go to Subnets.

Select Public-Subnet.

Click Actions → Edit subnet settings.

Enable Auto-assign Public IPv4 address.

Save.

# Step 5: Create Internet Gateway

Go to Internet Gateways.

Click Create Internet Gateway.

Name: My-IGW.

Click Create.

Now attach it:

Select My-IGW.

Click Attach to VPC.

Select My-VPC.

Attach.

# Step 6: Create Route Table for Public Subnet

Go to Route Tables.

Click Create Route Table.

Name: Public-RT.

Select My-VPC.

Click Create.

Now add route:

Select Public-RT.

Go to Routes → Edit routes.

Add route:

Destination:
0.0.0.0/0

Target:
Internet Gateway → My-IGW

Save changes.

# Step 7: Associate Public Subnet with Public Route Table

Open Public-RT.

Go to Subnet Associations.

Click Edit subnet association.

Select Public-Subnet.

Save.

# Step 8: Create Route Table for Private Subnet

Click Create Route Table.

Name: Private-RT.

Select My-VPC.

Click Create.

(No internet route added here.)

# Step 9: Associate Private Subnet with Private Route Table

Select Private-RT.

Go to Subnet Associations.

Click Edit subnet association.

Select Private-Subnet.

Save.

# Step 10: Launch EC2 in Public Subnet

Go to EC2 → Launch Instance.

Name: Public-Server.

AMI: Amazon Linux.

Instance type: t2.micro.

Key pair: create or select.

Network: My-VPC.

Subnet: Public-Subnet.

Auto assign public IP: Enable.

Security group: allow SSH (22) from anywhere.

Launch instance.

# Step 11: Launch EC2 in Private Subnet

Launch another instance.

Name: Private-Server.

Network: My-VPC.

Subnet: Private-Subnet.

Auto assign public IP: Disable.

Security group: allow SSH from VPC CIDR (10.0.0.0/16).

Launch.

# Step 12: Connect to Public Instance

From your terminal:

ssh -i key.pem ec2-user@Public-IP

# Step 13: Connect to Private Instance from Public Server

Inside the public server:

ssh -i key.pem ec2-user@Private-IP


Now the Public EC2 acts as a Bastion Host to reach the Private EC2.
