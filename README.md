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

# Step 13: Test Internet Connectivity from Public Instance

After logging into the public EC2 instance, check internet access.

ping google.com


You should see replies from Google.

Stop the command:

Ctrl + C


Why this is used:
This confirms the public subnet has internet connectivity through the Internet Gateway and public route table.

# Step 14: Ping Private Instance from Public Instance

Now try to ping the private instance IP.

ping <Private-Instance-Private-IP>


Example:

ping 10.0.2.10


You may see no response.

Stop the command:

Ctrl + C


Why this is used:
This shows that the private instance cannot be directly accessed yet, and we need SSH using the private key.

# Step 15: Create a File to Store the Private Key

Create a file using nano.

nano privatekey.pem


Paste the private key content inside the file.

Save the file:

Ctrl + O ENTER 

Ctrl + X


# Step 16: Change Permission of the Key

chmod 600 privatekey.pem


Why this is used:

SSH requires secure permissions for the key file.
chmod 600 allows only the owner to read and write the key.

# Step 17: SSH into the Private Instance

Connect to the private server.

ssh -i privatekey.pem ec2-user@<Private-Instance-IP>


Example:

ssh -i privatekey.pem ec2-user@10.0.2.10


Now you are logged into the private EC2 instance.

# Step 18: Ping Private Network

From the private instance, ping the public instance private IP.

ping <Public-Instance-Private-IP>


Example:

ping 10.0.1.15


Why this is used:
This confirms communication between subnets inside the same VPC.

Stop with:

Ctrl + C

# Step 19: Test Internet from Private Instance

Now try:

ping google.com


You will see no response.

Why this happens:
The private subnet route table does not have a route to the Internet Gateway, so the instance cannot access the internet. It is intentionally private for security.

# Step 16: Change Permission of the Key
chmod 600 privatekey.pem


Why this is used:
SSH requires secure permissions for the key file.
chmod 600 allows only the owner to read and write the key.

# Step 17: SSH into the Private Instance

Connect to the private server.

ssh -i privatekey.pem ec2-user@<Private-Instance-IP>


Example:

ssh -i privatekey.pem ec2-user@10.0.2.10


Now you are logged into the private EC2 instance.

# Step 18: Ping Private Network

From the private instance, ping the public instance private IP.

ping <Public-Instance-Private-IP>


Example:

ping 10.0.1.15


Why this is used:
This confirms communication between subnets inside the same VPC.

Stop with:

Ctrl + C

# Step 19: Test Internet from Private Instance

Now try:

ping google.com


You will see no response.

Why this happens:
The private subnet route table does not have a route to the Internet Gateway, so the instance cannot access the internet. It is intentionally private for security.
