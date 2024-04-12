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

- Step 1: Setup Resources in Azure
- Step 2: Ensure Connectivity between the client and Domain Controller
- Step 3: Install Active Directory
- Step 4: Create an Admin and Normal User Account in AD
- Step 5: Join Client-1 to your domain (mydomain.com)
- Step 6: Setup Remote Desktop for non-administrative users on Client-1
- Step 7: Create a bunch of additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>



![image](https://github.com/kphillip1/configure-ad/assets/165929885/24a6931b-3417-475b-933b-5b95e6c7258f)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/42af859d-60f3-4c70-b00e-68b02ea9b990)



Setup Resources in Azure
- Create the Domain Controller VM (Windows Server 2022) named “DC-1”
- Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
- Set Domain Controller’s NIC Private IP address to be static
- Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet 
- Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher)

![image](https://github.com/kphillip1/configure-ad/assets/165929885/7aa9345e-5222-45c7-8251-a28aee0d669e)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/0752b990-063f-472e-9ae6-fdf3ecefa373)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/98dbafbb-8635-4b09-acf6-77b7c53eb090)



Ensure Connectivity between the client and Domain Controller
- Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)
- Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
- Check back at Client-1 to see the ping succeed

![image](https://github.com/kphillip1/configure-ad/assets/165929885/03c7e6e1-4355-4a1d-a9bc-6fc90f962bc0)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/8568c054-77c5-4585-9b84-8363111e16a7)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/9d6b5d37-004b-4cca-8ab0-1e75f9ad031f)


Install Active Directory
- Login to DC-1 and install Active Directory Domain Services
- Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)
- Restart and then log back into DC-1 as user: mydomain.com\labuser

![image](https://github.com/kphillip1/configure-ad/assets/165929885/893ddf8a-4d15-45b9-bb03-af573ec86810)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/6ba52a91-03ee-429a-9380-08d8804809f3)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/ef5b7e5d-498a-4cb3-a73d-b43fe2928479)


Create an Admin and Normal User Account in AD
- In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
- Create a new OU named “_ADMINS”
- Create a new employee named “Jane Doe” (same password) with the username of “jane_admin”
- Add jane_admin to the “Domain Admins” Security Group
- Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
- User jane_admin as your admin account from now on

![image](https://github.com/kphillip1/configure-ad/assets/165929885/b78fec39-7612-43c0-a237-2c0e778f2a4d)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/e6d6227a-716a-4dcf-9d73-be4b5eb9f18c)



Join Client-1 to your domain (mydomain.com)
- From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
- From the Azure Portal, restart Client-1
- Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
- Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain
- Create a new OU named “_CLIENTS” and drag Client-1 into there (Step is not really necessary, just for organizational purposes.)

![image](https://github.com/kphillip1/configure-ad/assets/165929885/83463381-d72f-4441-9488-cbd6b2219fb0)

Setup Remote Desktop for non-administrative users on Client-1
- Log into Client-1 as mydomain.com\jane_admin and open system properties
- Click “Remote Desktop”
- Allow “domain users” access to remote desktop
- You can now log into Client-1 as a normal, non-administrative user now
- Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)

![image](https://github.com/kphillip1/configure-ad/assets/165929885/8d51f1d8-a4a8-4d8a-a9dd-6e498e3c9ac9)
![image](https://github.com/kphillip1/configure-ad/assets/165929885/171694d8-fa8f-43a5-9d09-5095639ab6d2)


Create a bunch of additional users and attempt to log into client-1 with one of the users
- Login to DC-1 as jane_admin
- Open PowerShell_ise as an administrator
- Create a new File and paste the contents of the script into it (https://github.com/kphillip1/configure-ad/blob/main/Generate-Names-Create-Users.ps1)
- Run the script and observe the accounts being created
- When finished, open ADUC and observe the accounts in the appropriate OU
- attempt to log into Client-1 with one of the accounts (take note of the password in the script -- "Password1")

![image](https://github.com/kphillip1/configure-ad/assets/165929885/2c97e5d8-9fff-44ec-922a-1dfcb010374f)

<h2 align="center"> Nice work! <br>Repeat this lab a couple times to build intuition for deploying Active Directory and creating users with a PowerShell script </h2>









