
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h2>On-premises Active Directory deployed in the Microsoft Azure</h2>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell
- Command Line

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create two virtual machines one running Windows Server (Domain Controller) and the other running Windows 10. Make sure the VMs are on the same virtual network.
- Set the Domain Controller to have a static private IP Address.
- Ensure connectivity between the two virtual machines. 
- Install Active Directory onto the Domain Controller.
- Create an Admin user and non-admin user in your Active Directory.
- Join your other virtual machine the client to the Domain.
- Setup your client to allow non-admin users to use remote desktop.
- Run a script to create many additional users to test remote desktop functionality.
![AD Model](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/92414fb7-c0b8-4d59-b2a1-a3b60d9b7e1f)

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/xiyEKiE.png"/>
</p>
<p>
Create two virtual machines in Azure; One with Windows server and the other running Windows 10. Ensure both are using two cores then ensure they are both on the same network using network watcher and using the topology feature shown on the left side of the screen in the Azure portal interface.
</p>
<br />

<p>
<img src="https://i.imgur.com/xNnthv2.png"/>
</p>
<p>
The virtual machine with Windows server installed will be the Domain Controller and is called DC-1 in this example. From the virtual machine page go to networking and then click the text next to network interface to continue with the demonstration.
</p>
<br />

<p>
<img src="https://i.imgur.com/i9Tk1uA.png"/>
</p>
<p>
From the network interface page click on ip configurations and then ipconfig1 to set the IP to static.
</p>
<br />

<p>
<img src="https://i.imgur.com/UquyD5f.png"/>
</p>
<p>
Open the client on remote desktop and from a command line type in ping -t and then the Domain Controller's private IP address. This will lead to a request time out. To enable traffic go onto the server and go to windows firewall with advance security maximize the window then click on inbound rules sort by protocol and then enable the first two ICMPv4 rules. 
</p>
<br />

<p>
<img src="https://i.imgur.com/omCtZl9.png"/>
</p>
<p>
To install active directory click on server manager and then to add roles and features.
</p>
<br />


<p>
<img src="https://i.imgur.com/2tFcTMb.png"/>
</p>
Click next until you get to this window and make sure to install Active Directory Domain Services.
<p>

<br />


<p>
<img src="https://i.imgur.com/Qf6W9N5.png"/>
</p>
<p>
Click on the flag in server manager and then promote to Domain controller. This will open this window click on add new forest and name your domain. You will have to set a password but we will not use it for this demonstration. This installation will require the VM to restart login in using yourdomain.com a '\' and then the username you made for the VM.
</p>
<br />


<p>
<img src="https://i.imgur.com/M9ivErk.png"/>
</p>
<p>
Once you've logged back into the Domaain Controllwer click on tools and then scroll to Active Directory and Users and then right click on your domain click new and create three organizational units: _EMPLOYEES, _ADMINS, _CLIENTS.
</p>
<br />


<p>
<img src="https://i.imgur.com/pmsijdP.png"/>
</p>
<p>
Go to the admin organizational unit you created and creat a new user by right clicking and going to new and then user and make sure to set the password to never expire. Use this account for the rest of the demonstration.
</p>
<br />

<p>
<img src="https://i.imgur.com/QJnNczQ.png"/>
</p>
Right click on the user you created and then properties and then member of to add the user to Domain Users.
<p>
</p>
<br />

<p>
<img src="https://i.imgur.com/8vzJHrK.png"/>
</p>
<p>
Go to the virtual machine that will be the client's network interface and then click on DNS Servers to set the DNS server to the private IP address of the Domain Controller.
</p>
<br />

<p>
<img src="https://i.imgur.com/We5DxP7.png"/>
</p>
<p>
Once you've done this restart the client and log back into it. Right click on the windows icon and click system and then rename the PC advanced to change the domain. Once you type in your domain use your admin login to join the client to the domain which will iniiate another restart.
</p>
<br />

<p>
<img src="https://i.imgur.com/0t5hCJs.png"/>
</p>
<p>
Login into the client with your admin account and go to the system settings and go to remoter desktop and the select users who can access this PC and add Domain Users to allow non-admin accounts to use remote desktop.
</p>
<br />

<p>
<img src="https://i.imgur.com/Am790Vu.png"/>
</p>
<p>
<a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
">Use this link</a> to copy a raw script and then open windows power shell ISE as an administrator from the Domain Controller. Click on file then new and then paste script and then click the green play button on top to run the script. This script requires you to have an OU named _EMPLOYEES. This will generate a numerous amount of accounts with the same password: password1. Use one of these accounts to login into your client to conclude the demonstration. 
</p>
<br />









