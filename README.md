<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources in Azure
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain
- Setup Remote Desktop for non-administrative users on Client-1
- Create a bunch of additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

![Screenshot 2024-03-13 at 5 59 09 PM](https://github.com/ory-it/configure-ad/assets/67742620/7f8309a9-87ea-4ea9-92d2-c9d13abe42cd)

<h3>Setup Resources in Azure</h3>

1. Create the Domain Controller VM (Windows Server 2022) named “DC-1”
  - Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
2. Set Domain Controller’s NIC Private IP address to be static
3. Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in Step 1.a
4. Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher


![Screenshot 2024-03-14 at 4 04 06 AM](https://github.com/ory-it/configure-ad/assets/67742620/bc75ffda-429c-4c90-9714-d955ee83d4b9)

<h3>Ensure Connectivity between the client and Domain Controller</h3>

5. Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)
6. Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
7. Check back at Client-1 to see the ping succeed

![Screenshot 2024-03-14 at 4 35 09 AM](https://github.com/ory-it/configure-ad/assets/67742620/908363b6-ac9a-456b-9514-6ed4b296e164)

<h3>Install Active Directory</h3>

8. Login to DC-1 and install Active Directory Domain Services
9. Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)
10. Restart and then log back into DC-1 as user: mydomain.com\labuser


![Screenshot 2024-03-14 at 4 43 18 AM](https://github.com/ory-it/configure-ad/assets/67742620/22d00672-3053-4dce-afbb-2f1b7d6ebff5)

<h3>Create an Admin and Normal User Account in AD</h3>

11. In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
12. Create a new OU named “_ADMINS”
13. Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
14. Add jane_admin to the “Domain Admins” Security Group
15. Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
16. User jane_admin as your admin account from now on


![Screenshot 2024-03-14 at 5 00 34 AM](https://github.com/ory-it/configure-ad/assets/67742620/0e457552-de26-4ace-9a45-68eda39ad1f9)

<h3>Join Client-1 to your domain</h3>

17. From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
18. From the Azure Portal, restart Client-1
19. Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
20. Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain
21. Create a new OU named “_CLIENTS” and drag Client-1 into there (Step is not really necessary, just for organizational purposes. I guess I skipped this in the lab!)


![Screenshot 2024-03-14 at 5 04 55 AM](https://github.com/ory-it/configure-ad/assets/67742620/439d1e0e-34d2-4c1e-8507-c9f19ff1f70e)


<h3>Setup Remote Desktop for non-administrative users on Client-1</h3>

22. Log into Client-1 as mydomain.com\jane_admin and open system properties
23. Click “Remote Desktop”
24. Allow “domain users” access to remote desktop
25. You can now log into Client-1 as a normal, non-administrative user now
26. Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)


![Screenshot 2024-03-14 at 5 07 28 AM](https://github.com/ory-it/configure-ad/assets/67742620/6c569d2e-da5c-4518-8515-46ab3b4e2507)

<h3>Create a bunch of additional users and attempt to log into client-1 with one of the users</h3>

27. Login to DC-1 as jane_admin
28. Open PowerShell_ise as an administrator
29. Create a new File and paste the contents of the script into it [](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
30. Run the script and observe the accounts being created
31. When finished, open ADUC and observe the accounts in the appropriate OU
32. Attempt to log into Client-1 with one of the accounts (take note of the password in the script)

