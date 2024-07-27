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
- step 6

<h2>Deployment and Configuration Steps</h2>

<h3>(1) Create two virtual machines (VM) in Azure.</h3>   

Create the Domain Controller VM (Windows Server 2022) named “DC-1”.   
![image](https://github.com/user-attachments/assets/ac14ac0e-e98c-45fa-919e-b39fd627c1c3)

<br>

Set Domain Controller’s NIC Private IP address to be static  
![image](https://github.com/user-attachments/assets/27cfb8de-2b85-4570-bc9e-70b82da6a0d2)

<br>

Create the Client VM (windows 10) named "Client-1".   
![image](https://github.com/user-attachments/assets/1944bf66-73d4-497f-b00a-a6a47acc16ff)   

Ensure that both VMs are in the same Vnet.  

<br>
<br> 

<h3>(2) Ensure Connectivity between the client and Domain Controller.</h3>   

Login to Client-1 with Remote Desktop using its public IP address   
![image](https://github.com/user-attachments/assets/98aaed1a-0717-490a-858f-ef858c294525)

<br>

Open Command Prompt -> ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)  
![image](https://github.com/user-attachments/assets/188a4543-f24f-43c8-ac5a-126525240282)   

The ping will time out.  So we're going to start up a second remote desktop with DC-1 to enable ICMPv4 on the local windows firewall.

<br>

Login to the Domain Controller  
![image](https://github.com/user-attachments/assets/cf802978-841e-4f0d-babf-94f9cf918a92)

<br>

Open "Windows Defender Firewall with Advanced Security"  
![image](https://github.com/user-attachments/assets/8bc7c87e-29f2-4a9f-904b-e7f43e25562b)

<br>

Click Inbound Rules -> click Protocol -> right click "Core Networking Diagnostics- ICMP Echo Request (ICMPv4-in)" -> click "Enable Rule"   
![image](https://github.com/user-attachments/assets/f1c99169-9159-4ffc-8d2f-3e8696ffdaba)   

<br> 

Now go back to Client-1 remote desktop and the ping should be successful.  
![image](https://github.com/user-attachments/assets/cff73e60-147d-42bf-9125-14a7f1b5c1bf)  
press ctrl+c to stop ping

<br>
<br> 

<h3>(3) Install Active Directory</h3>  


Click start -> go to "Server Manager" -> click "Add roles and features"  
![image](https://github.com/user-attachments/assets/6e7e294d-acf4-4ae8-a453-6296bf9e64c4)  

<br>

Click "Next" -> make sure "Role based or feature based installation" is selected and click "Next" -> click "Next" -> add "Active Directory Domain Services" and click "Next"  
![image](https://github.com/user-attachments/assets/3a45fcad-ea24-4f23-8520-42fb2d844ac2) 

<br> 

click "Next" until you can click "Install"  

![image](https://github.com/user-attachments/assets/ef190304-c7f9-4d04-839c-7dfbdfe75f8a)  

<br> 

Once installation is complete, click "close".  

Click the flag and "Promote this server to a domain controller"
![image](https://github.com/user-attachments/assets/bfb680b4-eaca-40e4-9560-1d176f4f9d90) 

<br>

Click "Add a new forest" -> create a "Root domain name" and click "Next"  
![image](https://github.com/user-attachments/assets/e28e6130-1934-43ce-b9b8-849bedda9b72)

<br>

Create a password and click "Next"   
![image](https://github.com/user-attachments/assets/a26b6531-34b9-468c-9a77-041a823a6b1d)

<br>

Make sure "Create DNS delegation" is not checked -> Click "Next" until you can click "install" 
![image](https://github.com/user-attachments/assets/f67ae219-428a-4a55-b2f8-b41a2c1028f4)

DC-1 is going to restart.   

Go back to DC-1 in Azure and refresh page -> copy the public ip -> paste it into remote desktop and click "Connect" -> click "More choices" -> click "Use a different account" -> enter the F.Q.D.N "fully qualified domain name"   
![image](https://github.com/user-attachments/assets/42a7b57e-c796-4b01-a0d2-95cf38fec56c)  

<br>
<br>

<h3>(4) Create two organizational units.  One named "_EMPLOYEES" the other named "_ADMINS".</h3>  


In DC-1 open Server manager -> open "Tools" -> click "Active Directory Users and Computers"    
![image](https://github.com/user-attachments/assets/9d8b981e-f2c1-4e33-995f-ab86a3989c0e)  

<br> 

We're going to create two Organizational units. One named "_EMPLOYEES" the other named "_ADMINS" 


Right click "mydomain.com" -> New -> Organizational units  
![image](https://github.com/user-attachments/assets/1498db0c-73d8-43a9-8cf2-acc76ba143c3)  

<br>

_EMPLOYEES   
![image](https://github.com/user-attachments/assets/d0468103-c84a-492f-b9b9-06d324816718)  


_ADMINS   
![image](https://github.com/user-attachments/assets/87706eea-782c-4128-ac9b-3d8472add3d7)   

<br>
<br>

<h3>(5) Create an Admin and Normal User Account in AD</h3>

Right click "_ADMINS" -> New -> User    
![image](https://github.com/user-attachments/assets/b02f0816-24c7-43af-b008-3adfb07b156c)  

<br>

![image](https://github.com/user-attachments/assets/bd523c35-5229-465d-9154-656e9831f4d6)   

<br>

For this example uncheck "User must change password at next logon"   
![image](https://github.com/user-attachments/assets/a9607848-d2f2-4bf7-88b8-d62069939522)   
Click "Next" -> click Finish"

<br>

Right click your name (in my case, Jordan Jefferson) -> Properties ->
