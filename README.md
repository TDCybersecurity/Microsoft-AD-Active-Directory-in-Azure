<p align="center">

</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />
![image](https://github.com/TDCybersecurity/configure-ad/assets/142702123/351eb3ca-493c-45a2-a673-3765c665c932)







<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell
- Command Line

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Creating two virtual machines, one running Windows Server (Domain Controller) and the other running Windows 10, both on the same virtual network.
- Set the Domain Controller to have a Static IP Address.
- Ensure connectivity between the two Virtual Machines.
- Install Active Directory onto the Domain Controller.
- Create an Admin user and non-admin user in Active Directory.
- Join your other Virtual Machine client to the Domain
- Setup your client to allow non-admin users to use Remote Desktop.
- Run a script to create many additional users to test remote desktop functionality.

<h2>Deployment and Configuration Steps</h2>

</p>
<p>
Create two Virtual Machines in Azure; one VM as a Windows Server and the other VM running Windows 10.  Ensure that both are using two cores that they are both on the same network using Network Watcher.  Additionally, to use the topology feature shown on the left side of the screen in the Azure portal interface.

</p>
<br />

<p>

</p>
<p>
The Windows Server virtual machine with the Domain Controller will be called DC-1.  From the virtual machine page go to networking and then click the text next to network interface to continue with the demonstration.
</p>
<br />

<p>

</p>
<p>
From the network interface page click on IP configurations and then ipconfig1 in order to set the IP Address to static.

Open the client on Remote Desktop and from a command line type in ping -t and then the Domain Controller's Private Ip Address.  This will lead to a request time out.  To enable traffic onto the Server and to the Windows Firewall with Advanced Security maximize the window and then click on Inbound Rules, sort by protocol and then enable the first two ICMPv4 rules.

To install Active Directory, click on Server Manager and then Add Rules or Features.

Click next until you get to this window and make sure to install Active Directory Domain Services.

Click on the flag in Server Manager and then promote to Domain Controller.  This will open this a window where you will click on Add a New Forest and Name your Domain.  You will have to set a password but we will not use it for this demonstration.  This installation will require the VM to restart.  Login using yourdomain.com a '/' and then the username you made for the VM

Once you've logged back into the Domain Controller click on tools and then scroll to Active Directory and Users and then right click on your Domain.  Click new and create three organizational units: _EMPLOYEES, _ADMINS, and _CLIENTS.

Go to the admin organizational unit you created and create a new user by reight clicking and going to new and then user and make sure to set the password to never expire.  Use this account for the rest of the demonstartion.

Richt click on the user you created and then properties and then member of to add the user to Domain Users.

Go the the Virtual Machine that will be the client's network interface and then click on DNS Server to set the DNS Server to the private IP Address of the Domain Controller.

Once that has been done restart the client and log back in.  Right click on the Windows icon and click system and rename the PC advanced to change the Domain.  Once you type in your Domain use your admin login to join the client to the domain which will initiate another restart.

Login to the client with your admin account and go to the system settings and go to remote desktop and select users who can access this PC and add Domain Users to allow non-admin accounts to use remote desktop.

Use this link to copy a raw script and then copen windows power shell ISE as an administrator from the Comonain Controller.  Click on file then new and then paste script and then click the green play button on top oto run the script.  This script requires yuou to have an OU names _EMPLOYEES.  Thjis will generate numerous accounts with password: password1.  Use one of these accounts to login into your client to conclude the demonstration.
</p>
<br />
