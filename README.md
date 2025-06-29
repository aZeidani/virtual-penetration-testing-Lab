You can follow this guide to build your emulation lab that can emulate cyber attacks on it.
--------------------------------------------------------------------------------
System and Network Administration Project
By: Anwar Ali Zeidani
This repository contains documentation for a comprehensive system and network administration project, detailing the setup, configuration, and security measures implemented across a virtualized network environment using GNS3.
Table of Contents
•
Project Overview
•
Part 1: Setting Up the GNS3 Environment
◦
GNS3 Installation & VM Import
◦
Network Topology & Router Configuration
•
Part 2: Windows Server Configuration (Active Directory & DHCP)
◦
Active Directory Domain Services (AD DS)
◦
DHCP Server Configuration
◦
Client Testing
•
Part 3: Ubuntu Linux Server Configuration (Apache & SSH)
◦
Internet Connectivity via NAT
◦
Apache2 Configuration
◦
SSH Service Configuration
◦
Service Testing
•
Part 4: Network Security & Testing
◦
Firewall Rules (Windows & Linux)
◦
Windows Server: Group Policy Implementation
◦
SNMP Service Installation
◦
Automated Backup Solution
•
Author
Project Overview
This project demonstrates the practical steps involved in setting up a network, configuring essential server roles (Active Directory, DHCP, Apache, SSH), and implementing various security measures within a virtualized lab environment. The setup utilizes GNS3 for network simulation and includes both Windows Server and Ubuntu Linux Server configurations.
Part 1: Setting Up the GNS3 Environment
GNS3 Installation & VM Import
To begin, you need to install the latest GNS3 version by visiting the official GNS3 website (https://gns3.com/software/download) and logging in to download the software. After installation, a new project is created. The project requires importing GNS3 appliances, including a Cisco c7200 router image, which can be downloaded from a specified GitHub repository (https://github.com/hegdepavankumar/Cisco-Images-for-GNS3-and-EVE-NG).
To import the router image:
•
Download the image.
•
Open GNS3 and navigate to Edit > Preferences > Dynamips > IOS routers.
•
Select new and choose your c7200_router_image.
•
Click Apply and exit. The router should then appear under "routers" on the left side of GNS3.
Network Topology & Router Configuration
The network topology for this project consists of:
•
Two switches
•
One router
•
Two client PCs
•
One Windows server
•
One Linux server
The router is configured by setting IP addresses for its interfaces. To access the router console, right-click on the router and select console.
•
Interface f0/0 is configured with IP 192.168.2.1.
•
Interface f2/0 is configured with IP 192.168.10.1. Always ensure configurations are correct and save them to NVRAM using the write command.
Part 2: Windows Server Configuration (Active Directory & DHCP)
Active Directory Domain Services (AD DS)
The Windows Server is configured with Active Directory Domain Services (AD DS) and promoted to a Domain Controller.
Installation Steps:
1.
Open Server Manager.
2.
Click Manage > Add Roles and Features.
3.
Select Role-based or feature-based installation.
4.
Ensure the local server is selected as the destination.
5.
Check the box for Active Directory Domain Services from the roles list. A dialog will prompt to Add features that are required for Active Directory Domain Services - click Add Features.
6.
Proceed through Features and AD DS Confirmation pages, then click Install.
Promotion to Domain Controller: Once AD DS is installed, promote the server:
1.
From the Add Roles and Features Wizard results, click Promote this server to a domain controller. Alternatively, use the yellow warning flag in Server Manager.
2.
In Deployment Configuration, select Add a new forest for a new Active Directory environment.
3.
Enter your desired Root domain name (e.g., gns3lab.local). It's recommended to use a .local suffix for internal networks.
4.
In Domain Controller Options:
◦
Choose the highest Forest functional level and Domain functional level supported.
◦
DNS server and Global Catalog (GC) options should remain checked by default.
◦
Do not check Read-only domain controller (RODC) for the first domain controller.
◦
Enter a strong Directory Services Restore Mode (DSRM) password for recovery purposes.
5.
Proceed through DNS Options (warnings about DNS delegation are normal for a new forest) and Additional Options.
6.
The wizard will perform a Prerequisites Check; resolve any errors before proceeding. Warnings about DNS delegation are expected.
7.
Click Install. The server will automatically restart after the promotion process completes.
DHCP Server Configuration
After AD DS, the DHCP Server role is installed on the Windows Server.
Installation Steps:
1.
Open Server Manager.
2.
Click Manage > Add Roles and Features.
3.
Select Role-based or feature-based installation and ensure the domain controller is selected.
4.
Check the box for DHCP Server. A dialog will appear to Add features that are required for DHCP Server - click Add Features.
5.
Proceed through Features and DHCP Server Confirmation pages, then click Install.
6.
Upon completion, click Complete DHCP configuration. Ensure the administrator account is selected for authorization and click Commit.
Create a DHCP Scope:
1.
Open DHCP Console via Server Manager > Tools > DHCP.
2.
Authorize the DHCP Server: Right-click on your server name, select Authorize, then Refresh (the red down arrow should turn green).
3.
Right-click on IPv4 and select New Scope....
4.
Enter a Scope Name (e.g., lan_network).
5.
Define the IP Address Range:
◦
Start IP address: 192.168.2.50
◦
End IP address: 192.168.2.100
◦
The Length/Subnet mask will auto-populate (e.g., 255.255.255.0 for a /24 network).
6.
Optionally add Exclusions.
7.
Set the Lease duration (e.g., 8 days).
8.
Choose Yes, I want to configure these options now.
9.
For Router (Default Gateway), enter IP address: 192.168.2.1 and click Add.
10.
For Domain Name and DNS Servers:
◦
The Parent domain should be your Active Directory domain name.
◦
Enter the IP address for your DNS server: 192.168.10.10 and click Add.
11.
If not using WINS, click Next.
12.
Select Yes, I want to activate this scope now.
13.
Click Finish.
Client Testing
The project demonstrates that a Parrot client successfully obtains an IP address (e.g., 192.168.2.51) from the Windows DHCP server. Connectivity between clients and the Windows server is verified.
Part 3: Ubuntu Linux Server Configuration (Apache & SSH)
Internet Connectivity via NAT
Before configuring Apache and SSH, the GNS3 network is connected to the internet using NAT.
1.
Deploy a NAT cloud from the left-hand pane in GNS3.
2.
Configure the router to route Internet traffic inside and outside the LAN.
◦
The router's f3/0 interface automatically obtains a DHCP IP from the NAT cloud (e.g., 12.168.152.129).
◦
Configure inside-outside NAT interfaces for f3/0, f0/0, and f2/0.
◦
Permit networks 192.168.10.0 and 192.168.2.0. This configuration proves that internet connectivity is established.
Apache2 Configuration
The Apache2 web server is installed and configured on the Ubuntu server.
1.
Install Apache2: $sudo apt install apache2 -y.
2.
Start the service: $sudo systemctl start apache2.
3.
Verify that Apache2 is active (running).
SSH Service Configuration
The SSH service is installed and configured on the Ubuntu server.
1.
Install SSH: $sudo apt install ssh -y.
2.
Modify the SSH configuration file: /etc/ssh/sshd_config. (Specific modifications are implied to allow certain groups, but details are not provided in the source excerpt for what was changed in sshd_config itself, only that AllowGroups was added.)
3.
Create users and a group:
◦
Create two users: linux_user and windows_user.
◦
Create a group named ssh_group.
◦
Assign both users to the ssh_group.
◦
Add ssh_group to the AllowGroups directive in the SSH configuration file.
4.
Set necessary permissions.
Service Testing
•
Nmap is used to check if Apache2 and SSH services are running on the Ubuntu server.
•
A Linux client is shown successfully visiting the webpage hosted on the Ubuntu server.
•
The linux_user is shown connecting to the Ubuntu server via SSH.
Part 4: Network Security & Testing
Firewall Rules (Windows & Linux)
Ubuntu Server (UFW & ModSecurity):
•
UFW rules are configured to only allow access to port 2200 (SSH) and port 80 (HTTP) from networks 192.168.2.0 and 192.168.10.0, denying any other connections. Ensure rules are ALLOW IN.
•
ModSecurity CRS (Web Application Firewall - WAF) is applied for enhanced security.
1.
Install ModSecurity CRS.
2.
Apply the security2 module.
3.
Restart Apache2 service and check its status; security2_module should appear, indicating it's enabled.
4.
Configure the core configuration file /etc/modsecurity/mod_security.conf by changing SecRuleEngine from DetectionOnly to On.
5.
Copy all core rule set (.conf) files from the rules folder to /etc/modsecurity/crs/. This enables ModSecurity to check all rules on every HTTP request.
6.
Check configuration syntax and restart the Apache2 service.
•
Apache HTTPS is configured by adding ssl-cert as an open-source certificate generator for web servers.
Windows Server Firewall:
•
Rules are added using Windows+R and wf.msc.
•
A rule to block specific ports is created by selecting block by Port and specifying the port to block.
•
The block the connection radio button is checked.
Windows Server: Group Policy Implementation
Group Policies are configured via Server Manager > Tools > Group Policy Management.
•
Navigate to Domains > gns3lab.local > right click > new.
•
Under Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy:
◦
Minimum password length is set.
◦
Password complexity is enabled.
◦
Account lockout is set after 5 failed attempts.
•
USB storage devices are disabled via Device Installation Restrictions:
◦
Computer Configuration > Policies > Administrative Templates > System > Device Installation > Device Installation Restrictions.
◦
The project indicates matching a drive ID for USBs.
•
Finally, policies are updated to refresh the server.
SNMP Service Installation
The SNMP service is installed via Server Manager.
•
The community name is set to a "complex string".
•
Allowed Management Stations are configured.
Automated Backup Solution
An Automated Backup Solution is implemented by installing Windows Server Backup.
•
This is done via Manage > Add Roles and Features > Select features page.
•
Scroll down and check the box next to Windows Server Backup.
•
Click Close once installation is complete.

--------------------------------------------------------------------------------
Author
Anwar Ali Zeidani

--------------------------------------------------------------------------------
