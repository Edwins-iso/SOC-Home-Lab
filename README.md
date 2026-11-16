<h1>SOC Home Lab with Azure</h1>

<h2>Description</h2>
(Documentation in progress) 

In this project, I set up a basic home SOC in Azure from scratch. Using a free Azure subscription, I will walk-through creating a virtual machine (VM), opening it to the internet as a honeypot, and forwarding logs to a central repository. Then, I will  integrate Microsoft Sentinel to analyze real-world attack data and create an attack map.

<br />


<h2>Utilities Used</h2>

- <b>Azure</b>
- <b>Microsoft Defender</b>
- <b>Microsoft Sentinel</b>

<h2>Environments Used </h2>

- <b>Windows 11</b>

<h2>Project Diagram:</h2>
<p align="center">
<img src="https://imgur.com/2k31BhO.png" height="80%" width="80%" alt="Home Lab Architecture"/>

<p align="center">

<h2>Project walk-through:</h2>

<p align="center">
Create Free Azure Subscription: <br/>
<img src="https://imgur.com/8PW6vLb.png" height="50%" width="50%" alt="Virtualbox Installer"/>

<p align="center">
For anyone without an Azure account. Microsoft does offer a free trial with 200 account credits. For a project like this, that is more than enough. Just be sure to not keep your VM running indefinitely. 
<br />
<br />
  
<h3>Part 1: Creating the Honeypot with an Azure Virtual Machine</h3>

<p align="center">
Go to: https://portal.azure.com and create a resource groups:<br/>
<img src="https://imgur.com/RB7fgg9.png" height="60%" width="60%" alt="VM Creation Prompt"/>

- Important Settings: Link the group to your Azure subscription and keep the region consistent with all future steps.
<br />
<br />
<p align="center">
Create a virtual network: <br/>
<img src="https://imgur.com/lY917nG.png" height="50%" width="50%" alt="Ubuntu Server Install"/>

- Most settings here can remain default.
- Just be sure to link the resource group to the resource network when prompted at this stage.
<br />
<br />
<p align="center">
Create the virtual machine:<br/>
<img src="https://imgur.com/7poZ3CY.png" height="60%" width="60%" alt="Internal Network IPV4 Properties Config"/>
<img src="https://imgur.com/qNliJRr.png" height="60%" width="60%" alt="Internal Network IPV4 Properties Config"/>
  
- Make sure to properly link the resource group and subscription,
- Name the VM something innocuous. In this case I chose JELLY-PLEX-EAST1.
- Keep the region consistent with the RG and VN.
- I chose a Windows 11 Image.
- For the Size, I just went with whatever was available for my subscription at 2vcpus, 8 GiB Memory. You may have to see all sizes to find this option.
- For networking, just match the information with your VN
<p align="center">
<img src="https://imgur.com/DnzOGf7.png" height="60%" width="60%" alt="Internal Network IPV4 Properties Config"/>

- Once complete, this is what your resource group should look like!
<br />
<br />
<p align="center">
Expose the honeypot:<br/>
<img src="https://imgur.com/2HDgTqd.png" height="60%" width="60%" alt="Internal Network IPV4 Properties Config"/>
  
- Open the Firewall Resource (Shield Icon)
- Delete the RDP inbound rule, then create a new inbound rule with the above settings
<p align="center">
<img src="https://imgur.com/yhBn48i.png" height="60%" width="60%" alt="Internal Network IPV4 Properties Config"/>

- Firewall rules should look like this!
<br />
<br />
<p align="center">
Remote into the VM:<br/>
<img src="https://imgur.com/NzHIzi3.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

- On windows, Start>Remote Desktop Connection.
- Enter the VM's Public IP Address and Connect.
- Enter the Admin account credentials that were created during the VM set-up to log-in.
<br />
<br />
<p align="center">
Disable the Firewall:<br/>
<img src="https://imgur.com/FTeJ0OU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/odVAvwq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/Br1ylM2.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

- Disable the Domain, Private and Public Profile Firewalls
<p align="center">
<img src="https://imgur.com/ywg7zAg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- Refresh and you should see that the Firewall is fully turned off.
<br />
<br />
<p align="center">
Confirm the machine is accessible to the open internet:<br/>
<img src="https://imgur.com/9giSf7u.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

  - On your host machine, the command prompt and ping the VM's public IP.
  - If the Ping is successfully returned, then we can proceed with setting up the log forwarding.
  - Close the Azure VM.
  
<br />
<br />

<h3>Part 2: Log Forwarding and KQL</h3>

<p align="center">
While connecting back into the Azure VM, Fail 3-5 log in attempts:<br/>
<img src="https://imgur.com/ebjfNrE.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
  
- Once complete, log back in correctly.
<br />
<p align="center">
Check the Event Viewer on the VM:<br/>
<img src="https://imgur.com/u6O9NBV.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

- Start Menu> Event Viewer> Windows Logs>Security.
- Select the top Item and ctrl+F to search for the failed login event ID (4625).
- You should see a log of your failed log in attempts.
- Now that we've confirmed logs are working properly, we can proceed with Log Forwarding!
<br />
<br />
<p align="center">
Create Log Analytics Workspace: <br/>
<img src="https://imgur.com/4h0WoXU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- In the Azure portal, Create a log analytics workspace using the above settings.
<br />
<br />
<p align="center">
Create Microsoft Sentinel:<br/>
<img src="https://imgur.com/1qCCE7C.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

- Add the Microsoft Sentinel to the Log Analytics Workspace
<br />
<br />
<p align="center">
Install Microsoft Security Events:<br/>
<p align="center">
<img src="https://imgur.com/Oyv6K0I.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- Within the Microsoft Sentinel Settings, Navigate to the content hub. This should direct you to the Windows Defender Content hub
<p align="center">
<img src="https://imgur.com/bmTQ0JU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- Search for the "Windows Security Events" Content and install.
<p align="center">
<img src="https://imgur.com/AnXQufs.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

- Once installed, the install button should now say manage. Click manage and once prompted, search for the "Windows and Security Events with AMA" Content.
<p align="center">
<img src="https://imgur.com/8Gyex21.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- Install it and then open connectors page
<p align="center">
<img src="https://imgur.com/MWni7Ea.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- Once inside, select "+Create data collection rule" at the bottom
<p align="center">
<img src="https://imgur.com/04iXCwS.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

- Rule Settings are pictured above
<p align="center">
<img src="https://imgur.com/DpCBd9V.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

- Back in Azure, you should be able to see the data collection rule listed as an extension in the VM's settings
- Now the VM's logs are connected to the Log Analytics Workspace!
<br />
<br />
<p align="center">
Querying Our Log Repository with KQL:<br/>
<img src="https://imgur.com/VscD44q.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

- In Azure, Navigate to the Log Analytics Workspace then open the Log tab.
- in the top right, Change the viewing mode from simple to KQL.
- then type "SecurityEvent"
- Run
- If nothing shows up check back every 30 minutes, repeat until queries show up.
- Once we start to see some queries, like shown above, that means the VM is officially under attack by hackers trying to gain access to the machine!
- Now let's find out where these hackers are coming from.
<br />
<br />

<h3>Part 3: Log Enrichment and Finding Location Data</h3>

<p align="center">
Import Sentinel Watch-list Spreadsheet<br/>
<img src="https://imgur.com/Yp7GWcs.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/BFamNVB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/V3egUau.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

- Download: geoip-summarized.csv 
- This file is a spreadsheet that summarizes the geographical locations tied to IP numbers. This watchlist will add more variables to the log columns, related to the attackers IP.
- Open Microsft Defender and search for "Watchlists", then create one.
- Configure settings as shown above and import the csv file when prompted to upload.
- Create!
<br />
<br />
<p align="center">
Import Sentinel Watch-list Spreadsheet<br/>
<img src="https://imgur.com/4Tnwl41.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/FD0i0ko.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/fnDt2Ao.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

- Back in Azure, copy any of the IP addresses of the attackers that have failed to log on and add it to the query below, in KQL mode.
- let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
- Observe the logs now have geographic information, so you can see where the attacks are coming from
<br />
<br />

<h3>Part 4: The Attack Map</h3>
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
