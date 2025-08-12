<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube:Deploy on-premises AD within Azure VMs ---on progress :'(](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setting Up the Domain Controller (Step 1) 
- Setting Up the Client Machine (Step 2)
- Testing the Connection (Step 3)
- Install Active Directory on DC-1 (Step 4)
- Create a Domain Admin User (Step 5)
- Join Client-1 to the Domain (Step 6)
- Enable Remote Desktop for Domain Users (Step 7)
- Bulk-Create Users with PowerShell and Test Login (Step 8)

<h2>Deployment and Configuration Steps</h2>

<p>
<img width="1667" height="847" alt="1 done" src="https://github.com/user-attachments/assets/9ef980b0-29e8-4697-9c8f-41fd55d57ffc" />

<p>

<h2>Setting Up the Domain Controller (DC-1)</h2>  
First things first...Head over to the Azure Portal and create a new Resource Group. This will be the home base for all your lab resources.

Next up, create a Virtual Network (VNet). This sets the stage for your virtual machines to talk to each other.

Now, spin up a new Windows Server 2022 VM and name it DC-1. Make sure it’s in the same region as your VNet. Pick a username and password you won’t forget, and maybe wright them down somewhere safe.

Once DC-1 is deployed:

Go into its network interface (NIC) settings.

Under IP configurations, change the private IP to Static so it doesn't change after reboots.

Connect to DC-1 using Remote Desktop.

Inside the VM, open Windows Firewall settings and temporarily disable the firewall...This makes it easier to test network connections later. (Don’t forget to turn it back on once you’re done testing.)

</p>
<br />

<p>
<img width="1099" height="903" alt="2 1" src="https://github.com/user-attachments/assets/5e43c737-3415-46fe-9ba7-60b41570fde3" />

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

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Install Active Directory on DC-1</h2>
Start by logging into DC-1, your Windows Server. Open Server Manager, and install the Active Directory Domain Services (AD DS) role.(only)

Once installed, promote the machine to a Domain Controller:

Choose "Add a new forest"

Use a domain name like mydomain.com (or whatever you want, just remember it)

Accept the defaults unless you know what you’re changing

Let the system reboot once setup is complete

After rebooting, log in using your domain account:

mydomain.com\xxxxxxxxx (x=user name), If you can’t log in after reboot, check your credentials and that the domain name is typed correctly.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Create a Domain Admin User</h2>
With the domain running, now it’s time to create a proper admin account.

Open Active Directory Users and Computers (ADUC) and do the following:

Create two new Organizational Units (OUs) by Right-click the mydomain.com > new > Organizational Unit:

_EMPLOYEES

_ADMINS

In _ADMINS, create a new user by Right-click the _ADMINS > new > user :

Name: Jane Doe

Username: jane_admin

Password: Cyberlab123! (or whatever you prefer)

Right-click the user, go to Properties > Member Of, and add her to:

Domain Admins (this gives full administrative access)

Once done, log off and log back into DC-1 using the new domain admin account:

mydomain.com\jane_admin
You’ll use this account for all admin tasks from now on.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Join Client-1 to the Domain</h2>

Now log into Client-1 using its local account (labuser) and join it to the domain:

Go to System > Rename this PC (Advanced)

Click "Change" and set the domain to mydomain.com

Provide domain info (e.g., jane_admin with your domain password)

Restart the machine when prompted

Back on DC-1, open ADUC and check that Client-1 shows up in Computers. Create a new OU (Organizational Unit) called _CLIENTS and move Client-1 into it for better organization.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Enable Remote Desktop for Domain Users</h2>
To allow non-admin users to remote into Client-1, log into Client-1 as jane_admin.

Open System Properties > Remote Desktop

Enable Remote Desktop

Click Select Users, then add:

Domain Users

This allows any domain user (not just admins) to connect via RDP.

 In real life, you would do this using Group Policy, but manual config is fine for a lab.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<h2>Bulk-Create Users with PowerShell and Test Login</h2>
Now let’s create a bunch of test users automatically.

Back on DC-1, log in as jane_admin, then:

Open PowerShell ISE as Administrator

Paste in a script that creates users — here’s a quick example:
 <a href="https://raw.githubusercontent.com/joshmadakor1/AD_PS/refs/heads/master/Generate-Names-Create-Users.ps1">Script!<a>
Run the script and check ADUC under _EMPLOYEES to confirm the accounts were created

To test, try logging into Client-1 with one of the new user accounts:

Username: mydomain.com\user you picked

Password: Password1

You should be able to remote access it, assuming RDP access for Domain Users was enabled earlier.



</p>
<br />
