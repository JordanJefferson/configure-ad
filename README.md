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
- Step 4

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

