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

<p align="center">
<img src="https://github.com/Aniki-The-Forbidden-One/Project-Azure-Network-Segmentation-with-RBAC-for-Secure-Access-Control/blob/main/Screenshots/(p1)%20VET%20and%20subnets.png?raw=true"/>
</p>

Next I'll create 3 NSGs so we can filter network traffic between the resources within our virtual networks: 
<p align="center">
<img src="https://github.com/Aniki-The-Forbidden-One/Project-Azure-Network-Segmentation-with-RBAC-for-Secure-Access-Control/blob/main/Screenshots/(p2)%20NSGs.png?raw=true"/>
</p>

Next we'll associate them with the appropriate subnets:

<p align="center">
<img src="https://github.com/Aniki-The-Forbidden-One/Project-Azure-Network-Segmentation-with-RBAC-for-Secure-Access-Control/blob/main/Screenshots/(p3)%20Subnets%20association.png?raw=true"/>
</p>

Next we'll apply some basic rules for each NSGs as followed:

App NSG - Deny all other inbound traffic at 200 piority, Allow port 80 (HTTP) and 443 (HTTPS) with 100 piority.

<img width="1707" height="438" alt="image" src="https://github.com/user-attachments/assets/22f70ef0-2623-487d-a475-b2cadc729967" />


Database NSG - Deny all other inbound traffic at 200 piority, Allow App to connect to database via port 3306 (MySQL) 
at 100 piority. *port 1433 if SQL Server*

<img width="1690" height="441" alt="image" src="https://github.com/user-attachments/assets/863cb004-679f-4da6-864c-c30f52b0f070" />


Management NSG - Deny all other inbound traffic at 200 piority, we will use Azure bastion for administrative access.

<img width="1705" height="403" alt="image" src="https://github.com/user-attachments/assets/20a57bd3-a8be-4332-8961-3bd0d9ab9e84" />

Before we continue i'll set up bastion for the VNET, we'll deploy some VMs later on.

<img width="979" height="475" alt="image" src="https://github.com/user-attachments/assets/ddf783a3-5ab1-47b1-aa4f-338f69e2f79d" />

Next I'll be setting up some security groups via Entra ID (formerly Azure AD).

<img width="1525" height="457" alt="image" src="https://github.com/user-attachments/assets/f439e55d-c7b0-4966-a58a-87badc069181" />

3 security groups will be set up. App Admins, Database Admins, Management Admins. This enforce the principle of least privilege, reducing risks from compromised credentials.
After this we shall use Access Control (IAM) to add role assignment for the security groups.
First we'll assign Contributor role to App admins from the resource group.

<img width="885" height="196" alt="image" src="https://github.com/user-attachments/assets/388bbde9-d3b7-4c36-ae4a-05ce9c4ce800" />

Next I'll assign the Contributor role for Database users. 
This is because we are using MySQL which does not have it's own contributor role at this moment of time.

<img width="872" height="262" alt="image" src="https://github.com/user-attachments/assets/9b374843-3a05-4b48-a832-d335852b2fd9" />

Finally we will assign Virtual Machine Contributor role to the Management Admins.

<img width="868" height="260" alt="image" src="https://github.com/user-attachments/assets/a041bbe6-77f5-4922-9ecd-77af9bcddd72" />

The next step is to assign some policies to the scope of the resource group.
We'll start with creating "Allowed Locations" with the parameters of "uksouth"

<img width="623" height="558" alt="image" src="https://github.com/user-attachments/assets/3ef20c7e-fbcb-4571-9869-c78882e1ff31" />

Next we'll do resource type policies. 1 for VMs, 1 for NSGs and another for MySQL.

<img width="729" height="537" alt="image" src="https://github.com/user-attachments/assets/9551f807-2a90-4434-8d6f-651bfdacecae" />

Now to allow storage for sotrage accounts through HTTPS protocol. We will use the storage accounts to store our flow logs.

<img width="617" height="587" alt="image" src="https://github.com/user-attachments/assets/4d037ca5-0a1c-48e1-b63b-abe075e1cbd5" />

We'll create a policy to ensure that every network security group will have flow logs.

<img width="640" height="555" alt="image" src="https://github.com/user-attachments/assets/18216c33-6eeb-42ac-b2a8-29afc59e96ba" />

I've ran into an issue with flow logs on NSGs which was giving me failed compliance.

<img width="1834" height="672" alt="image" src="https://github.com/user-attachments/assets/9ba83a5d-6f15-4616-9ab8-f1c968b36ba5" />

To deal with this I had to research the correct documentation on Microsoft's website and learn a better method.
https://learn.microsoft.com/en-us/azure/network-watcher/nsg-flow-logs-portal

<img width="1874" height="532" alt="image" src="https://github.com/user-attachments/assets/c4df2ed7-4316-4cd8-8523-9e86009d7b28" />

After confirming Green across the board on all policies I will now create new users for the groups created in Entra ID.

<img width="1142" height="557" alt="image" src="https://github.com/user-attachments/assets/896a1e5b-52e3-4656-bc2a-9e274d2eeaaa" />

The purpose of these user accounts are to test RBAC functionality to ensure the roles and permissions are working as intended.

<img width="1872" height="489" alt="image" src="https://github.com/user-attachments/assets/e7d0a422-019f-41c3-ba40-ec04cf64ab26" />

Now I will test to ensure the location policy created earlier works.

<img width="769" height="762" alt="image" src="https://github.com/user-attachments/assets/aa3e04e6-b4fd-4b46-a9de-64bf9bf2bd44" />

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

Topology of this project.

<img width="480" height="375" alt="image" src="https://github.com/user-attachments/assets/3f5693bc-a2a2-4ea0-9141-f686b7e4b7c0" />
