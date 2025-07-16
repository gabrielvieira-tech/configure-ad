<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

<h2>Setting Up the Domain Controller (DC-1)</h2>  
First things first — head over to the Azure Portal and create a new Resource Group. This will be the home base for all your lab resources.

Next up, create a Virtual Network (VNet). This sets the stage for your virtual machines to talk to each other.

Now, spin up a new Windows Server 2022 VM and name it DC-1. Make sure it’s in the same region as your VNet. Pick a username and password you won’t forget — and maybe wright them down somewhere safe.

Once DC-1 is deployed:

Go into its network interface (NIC) settings.

Under IP configurations, change the private IP to Static so it doesn't change after reboots.

Connect to DC-1 using Remote Desktop.

Inside the VM, open Windows Firewall settings and temporarily disable the firewall — this makes it easier to test network connections later. (Don’t forget to turn it back on once you’re done testing.)

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Setting Up the Client Machine (Client-1)</h2> 
With DC-1 ready, let’s create a client VM.

Create another VM, this time running Windows 10, and call it Client-1. Drop it in the same region and same VNet as DC-1 so they can communicate.

Choose your login credentials, then once it’s deployed:

Go to NIC settings for Client-1.

Update the DNS server to point to DC-1’s private IP, this lets Client-1 use DC-1 as its DNS server.

After updating the DNS, restart Client-1 from the Azure Portal to make the change valid.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Testing the Connection</h2>
With both VMs on and DNS configured, it’s time to test the setup.

Log into Client-1 via Remote Desktop. Open Command Prompt(cmd) and ping DC-1’s private IP, like this:

ping xxx.xxx.xxx.xxx change "x" for DC-1 private IP
If you see replies, you’re good to go — the machines are talking and DNS is working!

If it doesn’t work and you see a text like this "requested timeout", you should try:

Double-check that the firewall on DC-1 is off.

Make sure both VMs are on the same subnet and VNet.
</p>
<br />
