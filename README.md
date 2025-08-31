# AWS-site-to-site-VPN-Lab
The purpose of this Lab is to gain hands on experience with configuration of site-to-site vpn in AWS



## Initial Architecture

<img width="733" height="556" alt="PQa5Nhc6SSerWEODS2NV_STAGE1+-+Start+Architecture" src="https://github.com/user-attachments/assets/01f669f5-7685-4735-8535-de28370a3975" />


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

Had to rename the second gateway to onprem-router2 
<img width="1312" height="684" alt="customer-gateway-router2" src="https://github.com/user-attachments/assets/34d5e447-8288-41bf-9c5d-a012f4c6b15a" />


## Step 3: Create VPN attachment for the transit gateway

1. Create transit gateway attachment
2. select the transit gateway ID which was created from the CFN template
3. For customer gateway ID, select on-prem router 1
4. Enable Acceleration
5. Repeat these steps, but for on-prem router 2


<img width="1233" height="701" alt="tgw vpn attachment" src="https://github.com/user-attachments/assets/cee5071c-bcc9-4a09-831a-bf088392a909" />


## Step 4: Download the configuration files for each site-to-site vpn

1. Once the VPN attachments are created, navigate to the site-to-site vpn tab in the VPC console
2. You will see two site-to-site vpn attachments. Each associated with one of the customer gateways created previously
3. On the top right, download the configuration files for each site-to-site vpn and rename the files CONNECTION1CONFIG.txt and CONNECTION2CONFIG.txt

<img width="573" height="607" alt="VPN connection configuration" src="https://github.com/user-attachments/assets/e8f04bb3-1040-495d-adbc-704a1376425e" />


## Step 5: Collect the necessary details from the configuration files.
Here is a template of the information which we will need:

```
# SHARED VALUES

ROUTER1_PRIVATE_IP                  =   
ROUTER2_PRIVATE_IP                  =   
ONPREM BGP ASN                      = 65016  
AWS BGP ASN                         = 64512  

# CONNECTION1 - AWS => ON PREM ROUTER1

CONN1_TUNNEL1_PresharedKey          =  
CONN1_TUNNEL1_ONPREM_OUTSIDE_IP     =  
CONN1_TUNNEL1_AWS_OUTSIDE_IP        =  
CONN1_TUNNEL1_ONPREM_INSIDE_IP      =  
CONN1_TUNNEL1_AWS_INSIDE_IP         =  
CONN1_TUNNEL1_AWS_BGP_IP            =  

CONN1_TUNNEL2_PresharedKey          =  
CONN1_TUNNEL2_ONPREM_OUTSIDE_IP     =  
CONN1_TUNNEL2_AWS_OUTSIDE_IP        =  
CONN1_TUNNEL2_ONPREM_INSIDE_IP      =  
CONN1_TUNNEL2_AWS_INSIDE_IP         =  
CONN1_TUNNEL2_AWS_BGP_IP            =  


# CONNECTION2 - AWS => ON PREM ROUTER2

CONN2_TUNNEL1_PresharedKey          =  
CONN2_TUNNEL1_ONPREM_OUTSIDE_IP     =  
CONN2_TUNNEL1_AWS_OUTSIDE_IP        =  
CONN2_TUNNEL1_ONPREM_INSIDE_IP      =  
CONN2_TUNNEL1_AWS_INSIDE_IP         =  
CONN2_TUNNEL1_AWS_BGP_IP            =  

CONN2_TUNNEL2_PresharedKey          =  
CONN2_TUNNEL2_ONPREM_OUTSIDE_IP     =  
CONN2_TUNNEL2_AWS_OUTSIDE_IP        =  
CONN2_TUNNEL2_ONPREM_INSIDE_IP      =  
CONN2_TUNNEL2_AWS_INSIDE_IP         =  
CONN2_TUNNEL2_AWS_BGP_IP            =  

# INSTRUCTIONS ON GETTING THE VALUES

We will be locating values for a specific connection `CONN1` or `CONN2` and a specific tunnel .. `TUNNEL1` or `TUNNEL2`  

For anything starting with CONN1 .. Look in the `CONNECTION1CONFIG.TXT` file  
For anything starting with CONN2 .. Look in the `CONNECTION2CONFIG.TXT` file  
In each of the above files, for anything showing TUNNEL1 fine the section `IPSec Tunnel #1` in the above files (THE TOP HALF)  
In each of the above files, for anything showing TUNNEL2 fine the section `IPSec Tunnel #2` in the above files (THE BOTTOM HALF)  

For `ROUTER1_PRIVATE_IP` its the 192.168.12.SOMETHING Private IPv4 Address for `ROUTER1` - Check the `Outputs` of the `ONPREM` CFN Stack for `Private IP of Router1`  
For `ROUTER2_PRIVATE_IP` its the 192.168.12.SOMETHING Private IPv4 Address for `ROUTER2` - Check the `Outputs` of the `ONPREM` CFN Stack for `Private IP of Router2`  

For `CONN1_TUNNEL1_PresharedKey` open `CONNECTION1CONFIG.TXT`, Locate `IPSec Tunnel #1`, Locate `- Pre-Shared Key` Your key is there  
For `CONN1_TUNNEL2_PresharedKey` open `CONNECTION1CONFIG.TXT`, Locate `IPSec Tunnel #2`, Locate `- Pre-Shared Key` Your key is there  
For `CONN2_TUNNEL1_PresharedKey` open `CONNECTION2CONFIG.TXT`, Locate `IPSec Tunnel #1`, Locate `- Pre-Shared Key` Your key is there  
For `CONN2_TUNNEL2_PresharedKey` open `CONNECTION2CONFIG.TXT`, Locate `IPSec Tunnel #2`, Locate `- Pre-Shared Key` Your key is there  

For `CONN1_TUNNEL1_ONPREM_OUTSIDE_IP` its the PublicIPv4 Address of `ROUTER1`  
    `CONN1_TUNNEL2_ONPREM_OUTSIDE_IP` its the PublicIPv4 Address of `ROUTER1`  
    `CONN2_TUNNEL1_ONPREM_OUTSIDE_IP` its the PublicIPv4 Address of `ROUTER2`  
    `CONN2_TUNNEL2_ONPREM_OUTSIDE_IP` its the PublicIPv4 Address of `ROUTER2`  

For `CONN1_TUNNEL1_AWS_OUTSIDE_IP` open `CONNECTION1CONFIG.TXT`, locate `IPSec Tunnel #1`, locate `#3: Tunnel Interface Configuration`, locate `Outside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  
For `CONN1_TUNNEL2_AWS_OUTSIDE_IP` open `CONNECTION1CONFIG.TXT`, locate `IPSec Tunnel #2`, locate `#3: Tunnel Interface Configuration`, locate `Outside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  
For `CONN2_TUNNEL1_AWS_OUTSIDE_IP` open `CONNECTION2CONFIG.TXT`, locate `IPSec Tunnel #1`, locate `#3: Tunnel Interface Configuration`, locate `Outside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  
For `CONN2_TUNNEL2_AWS_OUTSIDE_IP` open `CONNECTION2CONFIG.TXT`, locate `IPSec Tunnel #2`, locate `#3: Tunnel Interface Configuration`, locate `Outside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  

For `CONN1_TUNNEL1_ONPREM_INSIDE_IP` open `CONNECTION1CONFIG.TXT`, locate `IPSec Tunnel #1`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Customer Gateway` the value is there  
For `CONN1_TUNNEL2_ONPREM_INSIDE_IP` open `CONNECTION1CONFIG.TXT`, locate `IPSec Tunnel #2`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Customer Gateway` the value is there  
For `CONN2_TUNNEL1_ONPREM_INSIDE_IP` open `CONNECTION2CONFIG.TXT`, locate `IPSec Tunnel #1`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Customer Gateway` the value is there  
For `CONN2_TUNNEL2_ONPREM_INSIDE_IP` open `CONNECTION2CONFIG.TXT`, locate `IPSec Tunnel #2`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Customer Gateway` the value is there  

For `CONN1_TUNNEL1_AWS_INSIDE_IP` open `CONNECTION1CONFIG.TXT`, locate `IPSec Tunnel #1`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  
For `CONN1_TUNNEL2_AWS_INSIDE_IP` open `CONNECTION1CONFIG.TXT`, locate `IPSec Tunnel #2`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  
For `CONN2_TUNNEL1_AWS_INSIDE_IP` open `CONNECTION2CONFIG.TXT`, locate `IPSec Tunnel #1`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  
For `CONN2_TUNNEL2_AWS_INSIDE_IP` open `CONNECTION2CONFIG.TXT`, locate `IPSec Tunnel #2`, locate `#3: Tunnel Interface Configuration`, locate `Inside IP Addresses:`, locate `- Virtual Private Gateway` the value is there  

For `CONN1_TUNNEL1_AWS_BGP_IP` the value is the same as `CONN1_TUNNEL1_AWS_INSIDE_IP`  (without the /30)  
For `CONN1_TUNNEL2_AWS_BGP_IP` the value is the same as `CONN1_TUNNEL2_AWS_INSIDE_IP`  (without the /30)    
For `CONN2_TUNNEL1_AWS_BGP_IP` the value is the same as `CONN2_TUNNEL1_AWS_INSIDE_IP`  (without the /30)    
For `CONN2_TUNNEL2_AWS_BGP_IP` the value is the same as `CONN2_TUNNEL2_AWS_INSIDE_IP`  (without the /30) 
```

### We will have two VPN connections, each with two tunnels connected to the accelerate VPN endpoints, then to the customer gateway

<img width="1324" height="610" alt="image" src="https://github.com/user-attachments/assets/9b2332dd-d381-4b32-83bd-a85e40401ee9" />


## Configuring on-premise strongswan to create IPSEC tunnels to AWS

In this section, we will be configuring each of the on premises Ubuntu, strongSwan Routers to create IPSEC tunnels to AWS. Each router will create 2 IPSEC tunnels, each going to a different AWS Endpoint.


### STAGE 3A - CONFIGURE IPSEC TUNNELS FOR ONPREMISES-ROUTER1

Move to EC2 Console
https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=instanceState
Click Instances on the left menu
Locate and select ONPREM-ROUTER1
Right Click => Connect
Select Session Manager
Click Connect

    sudo bash
    cd /home/ubuntu/demo_assets/
    nano ipsec.conf

This is is the file which configures the IPSEC Tunnel interfaces over which our VPN traffic flows.
As we are connected to Router 1 - This configures the ones for ROUTER1 -> BOTH AWS Endpoints

Replace the following placeholders with the real values in the DemoValueTemplate.md document

    ROUTER1_PRIVATE_IP
    CONN1_TUNNEL1_ONPREM_OUTSIDE_IP
    CONN1_TUNNEL1_AWS_OUTSIDE_IP
    CONN1_TUNNEL1_AWS_OUTSIDE_IP
    and
    ROUTER1_PRIVATE_IP
    CONN1_TUNNEL2_ONPREM_OUTSIDE_IP
    CONN1_TUNNEL2_AWS_OUTSIDE_IP
    CONN1_TUNNEL2_AWS_OUTSIDE_IP

ctrl+o to save and ctrl+x to exit

nano ipsec.secrets

This file controls authentication for the tunnels
Replace the following placeholders with the real values in the DemoValueTemplate.md document

    CONN1_TUNNEL1_ONPREM_OUTSIDE_IP
    CONN1_TUNNEL1_AWS_OUTSIDE_IP
    CONN1_TUNNEL1_PresharedKey
    and
    CONN1_TUNNEL2_ONPREM_OUTSIDE_IP
    CONN1_TUNNEL2_AWS_OUTSIDE_IP
    CONN1_TUNNEL2_PresharedKey

Ctrl+o to save
Ctrl+x to exit

nano ipsec-vti.sh

This script brings UP the IPSEC tunnel interfaces when needed
Replace the following placeholders with the real values in the DemoValueTemplate.md document

    CONN1_TUNNEL1_ONPREM_INSIDE_IP (ensuring the /30 is at the end)
    CONN1_TUNNEL1_AWS_INSIDE_IP (ensuring the /30 is at the end)
    CONN1_TUNNEL2_ONPREM_INSIDE_IP (ensuring the /30 is at the end)
    CONN1_TUNNEL2_AWS_INSIDE_IP (ensuring the /30 is at the end)

Ctrl+o to save
Ctrl+x to exit

cp ipsec* /etc
chmod +x /etc/ipsec-vti.sh

Now all the configuration for Router1 IPSEC has been completed, lets restart the strongSwan service to bring them up.

systemctl restart strongswan to restart strongSwan ... this should bring up the tunnels

We can check these tunnels are up by running
ifconfig
You should see vti1 and vti2 interfaces
You can also check the connection in the AWS VPC Console ...the tunnels should be down, but IPSEC should be shown as UP after a few minutes.

### STAGE 3B - CONFIGURE IPSEC TUNNELS FOR ONPREMISES-ROUTER2

(YOU WILL NEED THE CONNECTION2CONFIG.TXT) File you saved earlier

Move to EC2 Console
https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=instanceState
Click Instances on the left menu
Locate and select ONPREM-ROUTER2
Right Click => Connect
Select Session Manager
Click Connect

sudo bash
cd /home/ubuntu/demo_assets/
nano ipsec.conf

This is is the file which configures the IPSEC Tunnel interfaces over which our VPN traffic flows.
As we are connected to Router 2 - This configures the ones for ROUTER2 -> BOTH AWS Endpoints
Replace the following placeholders with the real values in the DemoValueTemplate.md document

    ROUTER2_PRIVATE_IP
    CONN2_TUNNEL1_ONPREM_OUTSIDE_IP
    CONN2_TUNNEL1_AWS_OUTSIDE_IP
    CONN2_TUNNEL1_AWS_OUTSIDE_IP
    and
    ROUTER2_PRIVATE_IP
    CONN2_TUNNEL2_ONPREM_OUTSIDE_IP
    CONN2_TUNNEL2_AWS_OUTSIDE_IP
    CONN2_TUNNEL2_AWS_OUTSIDE_IP

ctrl+o to save and ctrl+x to exit

nano ipsec.secrets

This file controls authentication for the tunnels
Replace the following placeholders with the real values in the DemoValueTemplate.md document

    CONN2_TUNNEL1_ONPREM_OUTSIDE_IP
    CONN2_TUNNEL1_AWS_OUTSIDE_IP
    CONN2_TUNNEL1_PresharedKey
    and
    CONN2_TUNNEL2_ONPREM_OUTSIDE_IP
    CONN2_TUNNEL2_AWS_OUTSIDE_IP
    CONN2_TUNNEL2_PresharedKey

Ctrl+o to save
Ctrl+x to exit

nano ipsec-vti.sh

This script brings UP the tunnel interfaces when needed
Replace the following placeholders with the real values in the DemoValueTemplate.md document

    CONN2_TUNNEL1_ONPREM_INSIDE_IP (ensuring the /30 is at the end)
    CONN2_TUNNEL1_AWS_INSIDE_IP (ensuring the /30 is at the end)
    CONN2_TUNNEL2_ONPREM_INSIDE_IP (ensuring the /30 is at the end)
    CONN2_TUNNEL2_AWS_INSIDE_IP (ensuring the /30 is at the end)

Ctrl+o to save
Ctrl+x to exit

cp ipsec* /etc
chmod +x /etc/ipsec-vti.sh

Now all the configuration for Router1 IPSEC has been completed, lets restart the strongSwan service to bring them up.

systemctl restart strongswan to restart strongSwan ... this should bring up the tunnels

We can check these tunnels are up by running
ifconfig
You should see vti1 and vti2 interfaces
You can also check the connection in the AWS VPC Console ...the tunnels should be down, but IPSEC should be shown as UP after a few minutes.
FINISH
When all 4 IPSEC TUNNELS are up, vti1 and vti2 on Router1 and Router2, the setup has been successful. There is now network connectivity between the ONPREM and AWS environments.

<img width="674" height="750" alt="image" src="https://github.com/user-attachments/assets/fc4d32a4-be51-45ea-9d5f-3a336a431d3b" />





