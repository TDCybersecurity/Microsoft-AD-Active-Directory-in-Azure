
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying Active Directory inside of Azure and Creating Users</h1>
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

<h2>This analogy may help you put this in perspective</h2>




Think of a **domain controller** like a **police station** in a town. Here's how it would work:

1. **Police Chief (Domain Controller):**Manages everything, knowing all the officers and their roles.
2. **Officer Check-in (Log Manager):** When officers start their shift, they check-in (attend role call) at the station to confirm their identity and get their assignments.
3. **Police Officers (Access Control):** Have different access levels. Some can access files, while others cannot, based on their clearance.
4. **Dispatcher (Organizer):** Coordinates resources and sends updates to all officers at once, just like pushing software updates to all computers.
5. **Admin Staff (IT Support):** If an officer forgets their assignment or loses their badge, admin staff can quickly update their records and get them back on track.

\* **Active Directory Group Policy** _ allows administrators to centrally manage and configure operating systems, applications, and user settings across a network.

<h2>1. Setup Resources in Azure</h2>

Create a **Resource Group** and name it **AD-Lab-B**

Create the **Domain Controller VM** with image **Windows Server 2022** named **DC-B**

Create the **Client VM** with **Windows 10 Pro** named **Client-B**

Make sure DC-A and Client-A are in the same **Virtual network/subnet**.

Change the **Domain Controller's vNIC Private IP Address** from Dynamic to **Static**

  1. Go to **portal.azure.com**\> Click on +Create a Resource\> Click on Virtual Machine\>Click on Create new.

1.2 Search virtual machines \> +Create\>Azure virtual machines: Create using this information.

| **Resource Group**| **Virtual Machine name**| **Region**| **Availability Options**| **Security Type**| **Image**| **Size**| **Username**| **Password**|
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| AD-Lab-B | DC-B | (US) East US2 | No infrastructure | Standard | **Windows Server** 2022 DataCenterAzure Edition -x64 Gen2 | Standard D4s v3 4 vcpus 16 GiB | Terry | Terry1234567 |

- [x] checked Would you like to use an existing Windows Server License?

- [x] checked I confirm I have an eligible Windows Server license with Software Assurance\* or Windows Server subscription to apply this Azure Hybrid Benefit. Then click **Review+create.**

  1. **Validation passed**. Next click **Create.** This will create your resources.  **Your deployment is complete.**
  2. Validation passed \> **Create**
![ADB1 4](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/6fc8d59e-dfee-42fb-b2ac-99065ba56164)

![](RackMultipart20240522-1-l8toj8_html_56e17eedb8f4aaaa.png)

<h2>2. Create another VM at Resource group selecing AD-Lab-B</h2>h2>
Create using this information. **make sure**  **Review + Create**

| **Resource Group**| **Virtual Machine name**| **Region**| **Availability Options**| **Security Type**| **Image**| **Size**| **Username**| **Password**|
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| AD-Lab-B | Client-B | (US)East US 2 | No infrastructure | Standard | **Windows 10** Pro version 22H2 | Standard E4s v3 4 vcpus 16 GiB | Terry | Terry1234567 |

2.1 Make sure to that virtual network\* is DC-B-vnet on the Networking tab. Click **Review + create.**

2.2 **Validation Passed**

  1. **Deployment is in Progress…while resources get created…then…**  **Your deployment is complete.**  **Go to Resources**

2.3 Set **Domain Controller's vNIC** from **Dynamic** to **Static**.

2.4 Go to DC-B\>Networking\>Network Settings\>NIC\>ipconfig1

2.5 Networking\>Network interface/IP configuration\> Edit IP configuration\> Static \> Save.
![ADB3 0](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/2c28edf4-181b-4bb6-8a51-e12e5aacc41e)
![ADB3 1](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/dacc6834-1683-4fbd-be1f-e7c86b6f11d4)




2.6 Observe information from both machines, confirm public and private IP addresses, and virtual network.

| **Virtual Machine**| **Public IP**| **Private IP**| **Virtual Network**|
| --- | --- | --- | --- |
| **DC-B**| **52.225.226.23**| **10.1.0.4**| **DC-B-vnet/default**|
| **Client-B**| **172.210.214.80**| **10.1.05**| **DC-B-vnet/default**|

<h2>3. Ensure Connectivity between the Client and the Domain Controller</h2>

3.1 Using RDC login to **Client-B****(IP 172.210.214.80) **and ping** DC-B's ****Private IP Address** with **ping -t**** 10.1.0.4 **which is a** perpetual ping **.** Request timed out**.

![ADB2 0ping](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/01f7f217-7d81-4fa8-b019-69ef303b4449)




3.2 Login to the **Domain Controller****(IP 52.225.226.23) **and** enable ICMPv4 **on** Inbound Rules **in the** local Window Firewall **. Type** wf.msc**in search bar _(Microsoft Common Console Document)_. Go to**Inbound Rules**

![ADB2Inbound Rules](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/7159df8e-19ec-4e71-b255-7ed95146e5f7)


3.3 Change both Echo Request Rules, so that **ENABLED** goes from NO to YES. Now you have ICMP.

![ADB icmp](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/a94f8d62-676c-4f91-97bc-eaec28dc7da6)


3.4 Go back to **Client-B** and observe command prompt **ping -t**.

Confirm that **ping -t** is working. **Reply from 10.1.0.4:bytes=32 time=1ms TTL=128 with the continuous ping switch.**

![AD3 3ping reply](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/f5f40232-15b7-4786-9b90-33be0c0176bd)


<h2>4. Install Active Directory</h2>

Login to DC-11 and install **Active Directory Domain Services**

4.1 Go to Server Manager Dashboard\>Tools\>

Promote as a DC: Setup a **new forest** as **mydomain.com**

Restart and then log back into the DC-1 as user **mydomain.com**

4.2 Log into **DC-2** and go to **Server Manager**.

Under Configure this local serve.

Click ****** Add roles and features** Next\>

Before You Begin Next\>

Installation Type Role-based Next\>

Server Selection Next\>

Server Roles select ****** Active Directory Domain Services** Next\>

Add features that are required for Active Directory Domain Services? Click Add Features

Select ****** Active Directory Domain Services** Next\>

Features _(no changes)_ Next\>

AD DS Next\>

Confirmation Install\> after installation Close

Check upper right corner of screen. Click yellow hazard sign.

4.3 Click on **Promote this server to a domain controller.**

4.4 Deployment Configuration popup. ****** Add a new forest **. At Root domain name:** mydomain.com **click** Next\>**

![ADB3 4](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/0e956cb8-f169-410b-8319-e441f4b81dd4)


4.5 Domain Controller Options: **Password**: **Password1**| **Confirm Password**: **Password1** click Next\>

![ADBPassword1](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/e1acc6a4-969a-4309-90ec-f08e4804e455)

4.6 DNS Options Next\>The NetBIOS domain name: **MYDOMAIN** _auto-populates in the box_ Next\>

Paths Next\>Review Options Next\>

Prerequisites Check\>Install and wait for Progress to change to This server was successfully installed.

![ADBInstallAD](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/e3f858fa-13ac-4e9c-ad31-7afee1d45e4a)

4.7 You're about to be signed out. You will lose your RDC connection and will need to sign back in.

![ADB4 7A](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/9f1c0861-82e2-411b-a770-d5683deccd4f)

![ADB4 7A](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/033ed29d-f7b1-4f6e-b4ab-f8cd9783caf0)



4.8 Reconnect to the RDC, in **DC-B** Refresh, copy Public IP and RDC again. Credentials. IPs stayed the same.



<h2>5. Create an Administrator and Normal User Account in (AD) Active Directory</h2>

In **Active Directory Users and Computers (ADUC),** create an **Organizational Unit (OU)** called " **\_EMPLOYEES**".

Create another OU named " **\_ADMINS**".

Create a new employee named " **Joe Doe**" (same password) with the username of " **Joe\_Admin**".

**Joe\_Admin will have Password1**

5.1 **Log in with the**** FQDN **which is** mydomain.com\Terry **and** Password Terry1234567. **** Don't miss this step.**

![ADBmydomain sign in](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/5c9c73f2-7aa5-4794-98cf-ea5b18a091c8)

![ADBAD menu](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/87b61480-5f36-49ba-8205-a8b3dfb143bb)


5.2 Right-click on **mydomain.com**\> Select **New**\> Select **Organizational Unit**

![ADB Admin user](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/7fb74bf9-8834-4a59-8f33-eccd92e4b911)

5.3 Create two organizations: \_EMPLOYEES and \_ADMINS and then refresh so the OU's go to the top of the list.

5.4 In mydomain.com go \_ADMINS\> then New\> then User

![ADB Employees](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/facb68ef-f657-422b-bd47-2421266e4211)
![ADB5 4 Admins](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/b20242b7-adb2-47b2-94d0-15cafd47bb54)
![ADB Open OU](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/0c672af9-5d2c-4afa-8cbb-3dc65eb6c22e)
![ADB Joe Doe](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/80ae7a73-abab-493e-8e39-2ce7d06e2c4a)
![ADB Password1](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/f7aaa4f2-2328-48cf-8101-3597bd11aaec)
![ADB Joe prop](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/b1f6d6ac-6f92-4f47-bfbd-147c4ad37451)
![ADB Domain Admins](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/447cfee2-fc2e-4305-bdb4-b2c27966d678)

Make Joe an Admin


5.5 Add Joe\_Admin to the **"Domain Admins" Security Group**. **Add\> Domain Admins\>OK\>Apply\>OK**

![ADB Rename this PC](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/da3b8544-8500-4c49-9643-65a9734fc494)

5.6 Logout/Close the connection to **DC-B** and log back in as " **mydomain.com\Joe\_Admin**" from now on.

5.7 Login as user **Joe\_Admin** with **Password1** as you Admin account from now on.

<h2>6. Join Client-B to your Domain (mydomain.com)</h2>

From the Azure Portal, set **Client-B DNS** setting to the DC-1 Private IP Address.

From the Azure Portal, **restart Client-B**

Login to **Client-B** as the original local admin **(labuser)** and join it to the domain (computer will restar

Login to the Domain Controller and **verify Client-B** shows up in ADUC.

Create a new OU Organizational Unit " **\_CLIENTS**" and drag **Client-B** into that folder.

6.0 RDC back into DC-B using the Public IP and Joe\_Admin as the user.

6.1 Open command line to confirm that you are using **Joe\_Admin**


6.2 Go Client-B and Restart.

6.3 Go to Client-B Networking > Network settings >

![ADB 6 3](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/81b9f91a-70d4-4d8d-ba2d-feb9d6f11bcc)

![ADB DNS](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/32348400-c147-4cc4-baf1-f5976daea297)



6.4 Restart **Client-B** from the Azure Portal to flush the DNS.

![ADB Client 1 restart](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/b68b4f54-506b-41ad-b24f-19f29eddbe21)


6.5 RDC into Client-B check **whoami**, **hostname**, and **ipconfig /all**. Confirm that the DNS is updated to the Private IP of the **Domain Controller**. i.e.,10.1.0.4
![ADB DNS private ip](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/2add720d-1ebf-4b4f-9d3a-359e91e0ace7)
Right Windows Start menu\>Systems\>Rename this PC\>Change\>Domain change to **mydomain.com**

6.6 Grant Joe\_Admin permission to join the domain.

![ADB Rename this PC](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/69020d12-cd4e-45c5-8ab8-6e546e29d4c2)

![](RackMultipart20240522-1-l8toj8_html_5dcaeb84c5209234.png)

<h2>7. Setup Remote Desktop for non-administrative users on Client-1</h2>

7.1 Log into Client-B as **mydomain.com\Joe\_Admin**

7.2 Open **System\>Remote Desktop\>User accounts\>Select users that can remotely access this PC properties**

7.3 Add\>enter domain user\>Check Names (it is underlined and confirmed)\>OK\> See it as new users\>OK (It will update)

7.4 Go back to DC-B as Joe\_Amin

7.5 Go to ADUC\>mydomain.com\>Users\>Domain Users\>Members. These members now have RDC access.

7.6 This is a screen hot of the members:
![ADB 7 6 Remote](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/b007b66a-e9f6-4999-b168-ba55b5add057)





<h2>8. Create additional users and attempt to log into Client-1 with one of the users.</h2>

Login to DC-B as Joe\_Admin

Open **PowerShell\_ISE**** Run as an Administrator**.

8.0 Go to  Start click on PowerShell ISE right click to Run As an Administrator

8.1 Click on File to open code editor

8.2 Go to lab notes and copy the code doc, come back to code editor and paste code. Reduce clients to 25.

![ADB 8 2 names](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/a7330dde-0a21-4a35-8e72-b81d47c4c951)

8.3 When finished, open _ADUC Active Directory Users and Computers_ and observe the accounts in the appropriate OU.

Select a random new user and use the wrong password 10 times. Go into ADUC to unlock and reset the password.

ADUC\>mydomain.com\>\_EMPLOYEES. Observe the names.

Take a random name **bacek.moju** and log into **Client-B** with Password as Password1.
![ADB 8 3](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/1137fb3a-b5f8-4967-81e3-bd8ffd1bfd98)


![](RackMultipart20240522-1-l8toj8_html_4a74f502aab3311d.png)

**8.4 bacek.moju accessing Client-B Virtual machine**

![adb 8 4 bacek](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/dbc78c34-a8f9-4fc4-8e32-8be315b0b74e)


Go to ExplorerThis PC\>C:Drive\>Users\> to see a log of who logged into the Virtual Machine.

![ADB 8 5](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/9694f094-aeb3-4d60-8079-1c4b84066a54)

8.5 Pick a Random employee **lab.texor**, go to **Client-B\>** do 11 failed logins to lock the account.

8.6 Go to **DC-B** Active Directory\>Go to employee\>Properties\>Account\> Unlock account.

8.7 Properties\>Reset Password\>New Password | Confirm password\> OK


![ADB8 7](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/f43393a9-42cd-4f9e-b778-3680f7680b41)
![ADB8](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/f3ea43a0-5384-42c6-9b4c-46bfc5ae593c)
![ADB 8 7c](https://github.com/TDCybersecurity/Microsoft-AD-Active-Directory-in-Azure/assets/142702123/cf73eddf-786f-4829-8470-34f4fb54b654)

<h2>Thank you for viewing my hands-on lab using Active Directory in Microsoft Azure.</h2>
<p>

</p>
<p>
<a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
">Use this link</a> to copy a raw script and then open windows power shell ISE as an administrator from the Domain Controller. Click on file then new and then paste script and then click the green play button on top to run the script. This script requires you to have an OU named _EMPLOYEES. This will generate a numerous amount of accounts with the same password: password1. Use one of these accounts to login into your client to conclude the demonstration. 
</p>
<br />









