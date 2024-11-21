# Project-Azure-Network-Segmentation-with-RBAC-for-Secure-Access-Control

Project: Azure Network Segmentation with RBAC for Secure Access Control

Objective:

Create a segmented Azure network with different subnets for application, database, and management layers. 
Using RBAC to control user access to resources within each network layer. 
This project demonstrates secure resource segmentation and precise access control within an Azure environment.

We'll start off this project by creating a VNET with 3 subnets, 1 for each layer. These layers will be App, Database and Management.
The IP for the subnets are as followed:
App_Subnet - 10.1.1.0/24
Database_Subnet - 10/1/2/0/24
Management_Subnet - 10.1.3.0/24

Next I'll create 3 NSGs and associate them with the appropriate subnets and apply some basic rules to the NSGs.
The basic rules for each NSGs are as followed:
App NSG - Deny all other inbound traffic at 200 piority, Allow port 80 (HTTP) and 443 (HTTPS) with 100 piority.
Database NSG - Deny all other inbound traffic at 200 piority, Allow App to connect to database via port 3306 (MySQL) 
at 100 piority. *port 1433 if SQL Server*
Management NSG - Deny all other inbound traffic at 200 piority, we will use Azure bastion for administrative access.

before we continue i'll set up bastion for the VNET, we'll deploy some VMs later on.

Next I'll be setting up some security groups via Entra ID (formerly Azure AD).
3 security groups will be set up. App Admins, Database Admins, Management Admins.

After this we shall use Access Control (IAM) to add role assignment for the security groups.
First we'll assign Contributor role to App admins from the resource group.
Next I'll assign the Contributor role for Database users. 
This is because we are using MySQL which does not have it's own contributor role at this moment of time.
Finally we will assign Virtual Machine Contributor role to the Management Admins.

The next step is to assign some policies to the scope of the resource group.
We'll start with creating "Allowed Locations" with the parameters of "uksouth"
Next we'll do resource type policies. 1 for VMs, 1 for NSGs and another for MySQL.

I've ran into an issue with flow logs on NSGs which was giving me failed compliance.
To deal with this I had to research the correct documentation on Microsoft's website and learn a better method.
https://learn.microsoft.com/en-us/azure/network-watcher/nsg-flow-logs-portal

After confirming Green across the board on all policies I will now create new users for the groups created in Entra ID.
The purpose of these user accounts are to test RBAC functionality to ensure the roles and permissions are working as intended.

Now that the project is finished, I will create ARM and Bicep templates for deployment purposes and upload to my Github.

Thank you for viewing my project.

Project Highlights
Security-first Approach: Enforced secure access using NSGs, HTTPS-only policies, and RBAC.
Scalable Design: Configured subnets and roles for easy scaling of resources.
Compliance Ready: Used Azure Policies to ensure consistent standards across the environment.

Challenges
Initial confusion with locating Azure policies such as "Require HTTPS for Storage Accounts."
Managing Azure Bastion costs and finding an alternative for management access.
Identifying the correct RBAC roles for MySQL databases.

Future Enhancements
Re-enable Azure Bastion for secure VM access when budget allows.
Expand RBAC roles to include automation tools (e.g., deploying CI/CD pipelines).
Add monitoring for cost management and budgeting.
