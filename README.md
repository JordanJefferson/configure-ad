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

- Create Virtual Machines (VM) in Azure
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create two organizational units
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain (mydomain.com)
- Create additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

<h3>(1) Create two virtual machines (VM) in Azure.</h3>   

Create the Domain Controller VM (Windows Server 2022) named “DC-1”.   
![image](https://github.com/user-attachments/assets/ac14ac0e-e98c-45fa-919e-b39fd627c1c3)

<br>

Set Domain Controller’s NIC Private IP address to be static.  
![image](https://github.com/user-attachments/assets/27cfb8de-2b85-4570-bc9e-70b82da6a0d2)

<br>

Create the Client VM (windows 10) named "Client-1".   
![image](https://github.com/user-attachments/assets/1944bf66-73d4-497f-b00a-a6a47acc16ff)   

Ensure that both VMs are in the same Vnet.  

<br>
<br> 

<h3>(2) Ensure Connectivity between the client and Domain Controller.</h3>   

Login to Client-1 with Remote Desktop using its public IP address.   
![image](https://github.com/user-attachments/assets/98aaed1a-0717-490a-858f-ef858c294525)

<br>

Open Command Prompt -> ping DC-1’s private IP address with ping -t <ip address> (perpetual ping).  
![image](https://github.com/user-attachments/assets/188a4543-f24f-43c8-ac5a-126525240282)   

The ping will time out.  So we're going to start up a second Remote Desktop with DC-1 to enable ICMPv4 on the local windows firewall.

<br>

Login to the Domain Controller.  
![image](https://github.com/user-attachments/assets/cf802978-841e-4f0d-babf-94f9cf918a92)

<br>

Open "Windows Defender Firewall with Advanced Security".  
![image](https://github.com/user-attachments/assets/8bc7c87e-29f2-4a9f-904b-e7f43e25562b)

<br>

Click Inbound Rules -> click Protocol -> right click "Core Networking Diagnostics- ICMP Echo Request (ICMPv4-in)" -> click "Enable Rule"   
![image](https://github.com/user-attachments/assets/f1c99169-9159-4ffc-8d2f-3e8696ffdaba)   

<br> 

Now go back to Client-1 Remote Desktop and the ping should be successful.  
![image](https://github.com/user-attachments/assets/cff73e60-147d-42bf-9125-14a7f1b5c1bf)  
Press ctrl+c to stop ping.

<br>
<br> 

<h3>(3) Install Active Directory.</h3>  


Click start -> go to "Server Manager" -> click "Add roles and features"  
![image](https://github.com/user-attachments/assets/6e7e294d-acf4-4ae8-a453-6296bf9e64c4)  

<br>

Click "Next" -> make sure "Role based or feature based installation" is selected and click "Next" -> click "Next" -> add "Active Directory Domain Services" and click "Next"  
![image](https://github.com/user-attachments/assets/3a45fcad-ea24-4f23-8520-42fb2d844ac2) 

<br> 

click "Next" until you can click "Install".  

![image](https://github.com/user-attachments/assets/ef190304-c7f9-4d04-839c-7dfbdfe75f8a)  

<br> 

Once installation is complete, click "close".  

Click the flag and "Promote this server to a domain controller".
![image](https://github.com/user-attachments/assets/bfb680b4-eaca-40e4-9560-1d176f4f9d90) 

<br>

Click "Add a new forest" -> create a "Root domain name" and click "Next".  
![image](https://github.com/user-attachments/assets/e28e6130-1934-43ce-b9b8-849bedda9b72)

<br>

Create a password and click "Next".   
![image](https://github.com/user-attachments/assets/a26b6531-34b9-468c-9a77-041a823a6b1d)

<br>

Make sure "Create DNS delegation" is not checked -> Click "Next" until you can click "install" 
![image](https://github.com/user-attachments/assets/f67ae219-428a-4a55-b2f8-b41a2c1028f4)

DC-1 is going to restart.   

Go back to DC-1 in Azure and refresh page -> copy the Public IP address -> paste it into Remote Desktop and click "Connect" -> click "More choices" -> click "Use a different account" -> enter the F.Q.D.N "fully qualified domain name"   
![image](https://github.com/user-attachments/assets/42a7b57e-c796-4b01-a0d2-95cf38fec56c)  

<br>
<br>

<h3>(4) Create two organizational units.  One named "_EMPLOYEES" the other named "_ADMINS".</h3>  


In DC-1 open Server manager -> open "Tools" -> click "Active Directory Users and Computers"    
![image](https://github.com/user-attachments/assets/9d8b981e-f2c1-4e33-995f-ab86a3989c0e)  

<br> 

We're going to create two Organizational units. One named "_EMPLOYEES" the other named "_ADMINS". 


Right click "mydomain.com" -> New -> Organizational units  
![image](https://github.com/user-attachments/assets/1498db0c-73d8-43a9-8cf2-acc76ba143c3)  

<br>

_EMPLOYEES   
![image](https://github.com/user-attachments/assets/d0468103-c84a-492f-b9b9-06d324816718)  


_ADMINS   
![image](https://github.com/user-attachments/assets/87706eea-782c-4128-ac9b-3d8472add3d7)   

<br>
<br>

<h3>(5) Create an Admin and Normal User Account in AD.</h3>

Right click "_ADMINS" -> New -> User    
![image](https://github.com/user-attachments/assets/b02f0816-24c7-43af-b008-3adfb07b156c)  

<br>

Now, fill in the Admins info.  It can be personal or generic (e.g., John Doe).   
![image](https://github.com/user-attachments/assets/bd523c35-5229-465d-9154-656e9831f4d6)   

<br>

For this example uncheck "User must change password at next logon".   
![image](https://github.com/user-attachments/assets/a9607848-d2f2-4bf7-88b8-d62069939522)   
Click "Next" -> click Finish"

<br>

Right click your name (in my case, Jordan Jefferson) -> Properties -> Member Of -> Add -> type "Domain Admins" -> Check Names -> OK   
![image](https://github.com/user-attachments/assets/b2c85941-3768-4c46-aeb5-285435a8cf8c)   

<br>

Click "Apply" then "OK".  
![image](https://github.com/user-attachments/assets/7ecc917d-612f-4a07-9eec-22903843230a)

<br>

Make sure you remember this login info, because we're about to log out of LabUser1 in DC-1.  

Open Command Prompt -> type "logoff" and click enter   
![image](https://github.com/user-attachments/assets/e226d27a-6697-4660-aa93-9b3c54464a38)  

<br>

Go back to DC-1 in Azure -> copy the Public IP address -> paste it into Remote Desktop and click "Connect" -> click "More choices" -> click "Use a different account" -> use "user logon name (pre-Windows 2000) to login     
![image](https://github.com/user-attachments/assets/fc1e8736-98ae-49d2-98af-1fe9f1eb2d71)  

<br>
<br>

<h3>(6) Join Client-1 to your domain (mydomain.com).</h3>


Copy DC-1 private IP address.   
![image](https://github.com/user-attachments/assets/928e89e2-1f9c-4c8c-a1c5-97d6e00c30af)
 
<br>

Type "Network interfaces" in Azure search bar.   
![image](https://github.com/user-attachments/assets/e50c4d6e-4512-4d77-b431-eff99fa2da94)

<br>

Select client-1245 -> DNS servers -> click "Custom" -> paste private IP address -> click "Save"     
![image](https://github.com/user-attachments/assets/39e0ed9a-4b67-4f83-af21-50084d9e3b19)   
After you click "Save", it should appear in "Applied DNS servers" after a short wait.

<br>

Go back to Client-1 in Azure and click restart.   
![image](https://github.com/user-attachments/assets/25baaa86-601a-40d5-b922-2267d8550fde)   

<br>

While on Client-1 in Azure -> copy the Public IP address -> paste ip into Remote Desktop and login as LabUser1   
![image](https://github.com/user-attachments/assets/ede3aef8-c5c3-4824-8fe9-74675b743487)   

<br>

open Command Prompt and type in "ipconfig /all" to confirm the DNS Servers is using DC-1 private IP address.    
![image](https://github.com/user-attachments/assets/90eef6f0-846a-49e1-a252-7d6ab243a7c7)   

<br>   

Right click start -> click "System" -> click "Rename this PC (advanced)" -> click "Change" -> click "Domain" -> type in "mydomain.com" -> Click "OK"   
![image](https://github.com/user-attachments/assets/0757587f-f561-4052-8c7b-728566959ece)   

<br>

Type in Admin account info   
![image](https://github.com/user-attachments/assets/eb5c92a3-6a7a-445a-a391-53d6ba0ecd5e)   

<br>

Restart Client-1 VM and log back in as Admin.   
![image](https://github.com/user-attachments/assets/e57b0dee-2c36-4a15-91f6-a8796291c7c8)   

<br>

Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain.  

<br>

Create a new Organizational Unit named “_CLIENTS” and move Client-1 into there.   
![image](https://github.com/user-attachments/assets/2221f36c-a8c8-4684-b2c8-f405a818728c)   

<br>
<br>

<h3>(7) Setup Remote Desktop for non-administrative users on Client-1.</h3>

Come back to Client-1 Remote Desktop and Right click start -> click "System" -> click "Select users who can remotely access this PC" -> click "Add" -> type "Domain Users" -> Click "Check Names" -> click OK   
![image](https://github.com/user-attachments/assets/80b265e8-af07-49f7-a736-951dfacd378b)   
Click "OK" one more time.   

<br>

You can now log into Client-1 as a normal, non-administrative user now.

<br>
<br>

<h3>(8) Create additional users and attempt to log into client-1 with one of the users.</h3>  

Login to DC-1 as jordan_admin.   

Open PowerShell_ise as an administrator.   

Create a new File and paste the contents of the script into it. (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)   

click "Run Script" to begin downloading 10,000 users.
![image](https://github.com/user-attachments/assets/b5e7367b-6aa7-45fb-96e4-c6e923c07102)   

<br>

Open "Active Directory Users and Computers" and observe the new user accounts.   
![image](https://github.com/user-attachments/assets/69bedcb0-e553-449a-9bbe-2e425e541010)   

<br>

Attempt to log into Client-1 with one of the accounts (the password is "Password1").   

I'll be using "base.wes" to login.    
![image](https://github.com/user-attachments/assets/a5faf500-0152-4280-97d3-a735518c129c)  

<br>

sign out of DC-1 to log back in as "base.wes".   
![image](https://github.com/user-attachments/assets/982df183-ce04-498d-9096-2dd9e0f5b87c)   

![image](https://github.com/user-attachments/assets/acf4b460-c46a-42bf-93ec-8bd781cff116)


<br>
<br>

<h2>Congratulations!! You've successfully implemented Active Directory in Azure VMs.</h2>  
In the next lab we're going to simulate using Active Directory as a Tech Support Profesional.
