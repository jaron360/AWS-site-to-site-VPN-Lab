# AWS-site-to-site-VPN-Lab
The purpose of this Lab is to gain hands on experience with configuration of site-to-site vpn in AWS



## Initial Architecture

<img width="1433" height="756" alt="PQa5Nhc6SSerWEODS2NV_STAGE1+-+Start+Architecture" src="https://github.com/user-attachments/assets/01f669f5-7685-4735-8535-de28370a3975" />


## Step 1: Deploy the CloudFormation template 
The CFN template creates the following:
- VPC,
- subnets
- route tables
- EC2 instances for simulated on-premise servers
- EC2 instances for simulated AWS servers
- Instance profiles/permissions
- Elastic IP addresses which are used for the on-premise routers
- Transit Gateway

## Step 2: Create and configure the simulated on-premise routers/customer gateways

1. Create two customer gateways.
2. In each customer gateway, specify the IP address of the router 1 and router 2 which can be found in the outputs section of the CFN template.
3. Specify the BGP ASN as 65016 for both customer gateways


<img width="1354" height="666" alt="customer-gateway-router1" src="https://github.com/user-attachments/assets/9b80d9cf-9ac7-4f85-8e8f-987b3c5149fe" />


<img width="1312" height="684" alt="customer-gateway-router2" src="https://github.com/user-attachments/assets/34d5e447-8288-41bf-9c5d-a012f4c6b15a" />

