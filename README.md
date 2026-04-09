# Aws-Secure-2-Tier-VPC
Secure 2-tier AWS VPC architecture with public/private subnets, NAT Gateway, and EC2 instances.
Project Overview
In this project, I built a secure 2-tier network environment using the AWS Console.
The goal was to create a network with both public and private access while keeping internal resources secure.
I created and configured:
•	Virtual Private Cloud (VPC)
•	Public and Private Subnets
•	Internet Gateway (IGW)
•	Route Tables
•	NAT Gateway
•	Security Groups
•	EC2 Test Instances
I also verified which subnet was public and which was private.
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/Two-tier%20AWS%20architecture%20diagram.png)
________________________________________
Creating the VPC
A Virtual Private Cloud (VPC) is an isolated private network in AWS.
I created a VPC using the IPv4 CIDR block:
10.0.0.0/16
This range allows enough IP addresses for future resources.
I enabled:
•	DNS Resolution
•	DNS Hostnames
These settings are important because they allow EC2 instances to have usable DNS names.

![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/00b2dc9fd0f76d3d9f824032f9e315b43facb82d/vpc%20created%20tea.png)
________________________________________
Creating Public and Private Subnets
Next, I created two subnets:
•	Public Subnet
•	Private Subnet
A subnet is a smaller network inside a VPC.
For the public subnet, I enabled:
Auto-assign Public IPv4 Address
This ensures that any EC2 instance launched in the public subnet automatically receives a public IP address.
The difference between the subnets:
•	Public Subnet: Can communicate directly with the internet
•	Private Subnet: Cannot communicate directly with the internet
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/public%20and%20private%20subnet%20.png)

________________________________________
Creating the Internet Gateway (IGW)
An Internet Gateway allows communication between the VPC and the internet.
Steps completed:
1.	Created the Internet Gateway
2.	Attached it to the VPC
This allowed the public subnet to communicate with the internet.

![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/3fd9d765e2108dbb9480de4dddc204841a644019/internet%20gateway.png)
________________________________________
Creating Route Tables
Route tables control where network traffic is sent.
I created a public route table and added this route:
Destination: 0.0.0.0/0
Target: Internet Gateway
This allows internet traffic to reach the public subnet.

![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/route%20table%201.png)

Then I associated the public route table with the public subnet.
At this stage, the private subnet still only routed traffic inside the VPC (10.0.0.0/16).

![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/subnet%20association.png)
________________________________________
Creating the NAT Gateway
A NAT Gateway allows private subnets to access the internet safely without exposing them to inbound internet traffic.
Important configuration:
•	The NAT Gateway was created inside the public subnet
•	An Elastic IP address was assigned to it
After creating the NAT Gateway, I updated the private route table and added:
Destination: 0.0.0.0/0
Target: NAT Gateway
This allowed the private subnet to access the internet for outbound traffic only.

![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/nat%20gateway.png)
________________________________________
Creating Security Groups
Security Groups act as virtual firewalls that control traffic to EC2 instances.
I created two security groups:
Public Security Group
Inbound Rules:
•	SSH (Port 22) — Allows remote login
•	HTTP (Port 80) — Allows web traffic
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/public%20sg.png)
________________________________________
Private Security Group
Inbound Rules:
•	SSH (Port 22) — Allows SSH access only from the public security group
This setup allows secure SSH access from the public EC2 instance to the private EC2 instance without exposing the private instance to the internet.
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/private%20sg.png)

________________________________________
Launching EC2 Instances
An EC2 instance is a virtual computer in the cloud.
I launched:
•	One EC2 instance in the public subnet
•	One EC2 instance in the private subnet
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/public%20ec2%20instance.png)
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/private%20ec2%20instance.png)
Important settings:
•	Public EC2 instance: Assigned a public IP
•	Private EC2 instance: Public IP disabled to keep it secure
If the private instance needs internet access, it uses the NAT Gateway.
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/nat%20gateway%20in%20public%20subnet.png)
________________________________________
Testing Connectivity
I tested SSH access using Git Bash.
At first, I received this error:
ssh: connect to host port 22: Connection timed out
This happened because I accidentally added an extra number to the IP address.
After correcting the IP address, the connection worked successfully.
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/ssh.png)
________________________________________
Testing Internet Access from Private Instance
To confirm that the NAT Gateway was working, I tested internet connectivity from the private EC2 instance using:
curl -I https://www.amazon.com
The command worked successfully, confirming that the private instance could access the internet through the NAT Gateway.
![image alt](https://github.com/teajo99/Aws-Secure-2-Tier-VPC/blob/77e3124a73a8fefb2f87b91b305e14a8328446fe/internet%20connection.png)
________________________________________
Summary
In this project, I successfully built a secure 2-tier AWS network architecture.
Key achievements:
•	Created a VPC with public and private subnets
•	Connected the VPC to the internet using an Internet Gateway
•	Configured a NAT Gateway for secure outbound internet access
•	Set up route tables to control traffic flow
•	Implemented security groups to restrict access
•	Launched EC2 instances in both tiers
•	Verified connectivity between instances
•	Tested internet access from the private subnet
This project helped me understand how secure cloud networks are designed and how different components work together.

