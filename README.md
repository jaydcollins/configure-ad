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
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Setup Resources in Azure
- Step 2: Ensure Connectivity between the client and Domain Controller
- Step 3: Install Active Directory
- Step 4: Create an Admin and Normal User Account in AD
- Step 5: Join Client-1 to your domain (mydomain.com)
- Step 6: Setup Remote Desktop for non-administrative users on Client-1
- Step 7: Create a bunch of additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>



<img width="949" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/8cb9f035-a553-4182-a4a6-960ea6f965da">
<img width="944" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/114b8455-5a25-456e-ba7e-78620e7d5d2f">




Setup Resources in Azure
- Create the Domain Controller VM (Windows Server 2022) named “DC-2”
- Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
- Set Domain Controller’s NIC Private IP address to be static
- Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet 
- Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher)

<img width="673" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/6122d5d8-b6c6-4c7d-8990-fb622dad9f13">
<img width="773" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/a90fceeb-93db-40f3-b11c-a438647a78b3">
<img width="674" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/c670e126-e025-4b16-b705-f64c8550228c">




Ensure Connectivity between the client and Domain Controller
- Login to Client-1 with Remote Desktop and ping DC-2’s private IP address with ping -t <ip address> (perpetual ping)
- Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
- Check back at Client-1 to see the ping succeed

<img width="586" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/1035d5c7-eb42-43e2-8db0-f4baa4689fc7">

<img width="572" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/701c2e94-99d7-40cd-864c-2f6afd8cb1dd">



Install Active Directory
- Login to DC-2 and install Active Directory Domain Services
- Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)
- Restart and then log back into DC-2 as user: mydomain.com\labuser

<img width="821" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/a7fe1542-48c9-4bf4-9374-8ec79641bdc5">

<img width="327" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/5f658d7c-c895-43c6-9570-0a36ee94f84e">

<img width="824" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/58ac6b44-209a-43de-9447-2a9265f7395a">



Create an Admin and Normal User Account in AD
- In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
- Create a new OU named “_ADMINS”
- Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
- Add jane_admin to the “Domain Admins” Security Group
- Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
- User jane_admin as your admin account from now on

<img width="560" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/107e9d25-2c2a-4946-bf4f-e4f7c21409de">

<img width="307" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/e034ceac-5f52-4338-9811-0708e0d71c45">




Join Client-1 to your domain (mydomain.com)
- From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
- From the Azure Portal, restart Client-1
- Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
- Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain
- Create a new OU named “_CLIENTS” and drag Client-1 into there (Step is not really necessary, just for organizational purposes.)

<img width="604" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/1cb7e27f-7b88-4a83-a54c-705dbd4f01e4">


Setup Remote Desktop for non-administrative users on Client-1
- Log into Client-1 as mydomain.com\jane_admin and open system properties
- Click “Remote Desktop”
- Allow “domain users” access to remote desktop
- You can now log into Client-1 as a normal, non-administrative user now
- Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)

<img width="713" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/23a41a48-7951-40c5-9e1e-acb8b6aba055">

<img width="701" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/b6260614-f2f7-49ab-8693-80c962e267e5">



Create a bunch of additional users and attempt to log into client-1 with one of the users
- Login to DC-1 as jane_admin
- Open PowerShell_ise as an administrator
- Create a new File and paste the contents of the script into it (https://github.com/kphillip1/configure-ad/blob/main/Generate-Names-Create-Users.ps1)
- Run the script and observe the accounts being created
- When finished, open ADUC and observe the accounts in the appropriate OU
- attempt to log into Client-1 with one of the accounts (take note of the password in the script -- "Password1")

<img width="704" alt="image" src="https://github.com/jaydcollins/configure-ad/assets/164976272/1864ac65-836e-4b4b-b7b5-44b132dd123a">


<h2 align="center"> Nice work! <br>Repeat this lab a couple times to build intuition for deploying Active Directory and creating users with a PowerShell script </h2>









