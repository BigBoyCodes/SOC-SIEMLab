<h1>SOC + SIEM (Monitoring Live Attacks)</h1>


<h2>Description</h2>
<b>In this lab, I was able to  set up a basic home SOC in Azure from scratch. By creating a virtual machine (VM), opening it to the internet as a honeypot, and forwarding logs to a central repository and  then finally  integrate Microsoft Sentinel to analyze real-world attack data.
</b>
<br />
<br />
<b>Part 1. Create the Honey Pot (Azure Virtual Machine)</b>

Create a new Windows 10 virtual machine. 
<p align="center">
<img src="https://i.imgur.com/wgeLfAv.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>


Go to the Network Security Group for your virtual machine and create a rule that allows all traffic inbound

<p align="center">
<img src="https://i.imgur.com/1kVGmm9.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>


Log into your virtual machine and turn off the windows firewall (start -> wf.msc -> properties -> all off)

<br />
<br />

<b>Part 2. Logging into the VM and inspecting logs</b>

Fail 3 logins as “employee” (or some other username)

Login to your virtual machine

Open up Event Viewer and inspect the security logs

See the 3 failed logins as “employee”, event ID 4625

Next, we are going to create a central log repository called a LAW

<br />
<br />

<b>Part 3. Log Forwarding and KQL</br>

Create Log Analytics Workspace

<p align="center">
<img src="https://i.imgur.com/djl6UN5.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>

Create a Sentinel Instance and connect it to Log Analytics

<p align="center">
<img src="https://i.imgur.com/IL5ZjOl.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>


(observe architecture)

Configure the “Windows Security Events via AMA” connector
<p align="center">
<img src="https://i.imgur.com/pncMomq.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>



Create the DCR within sentinel, watch for extension creation
https://i.imgur.com/pncMomq.png
<p align="center">
<img src="https://i.imgur.com/B4KP98F.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>




Query for logs within the LAW
<p align="center">
<img src="https://i.imgur.com/CXD8kDC.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>




<br />
<br />

<b>Part 4. Log Enrichment and Finding Location Data</b>

Observe the SecurityEvent logs in the Log Analytics Workspace; there is no location data, only IP address, which we can use to derive the location data.

We are going to import a spreadsheet (as a “Sentinel Watchlist”) which contains geographic information for each block of IP addresses.

Download: <a href = "https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view?usp=sharing"> geoip-summarized.csv</a>

Within Sentinel, create the watchlist:

Name/Alias: geoip
Source type: Local File
Number of lines before row: 0
Search Key: network

<p align="center">
<img src="https://i.imgur.com/kP5aC6J.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>




Allow the watchlist to fully import, there should be a total of roughly 54,000 rows.

In real life, this location data would come from a live source or it would be updated automatically on the back end by your service provider.

(observe architecture)

Observe the logs now have geographic information, so you can see where the attacks are coming from

let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents


(observe architecture)
https://i.imgur.com/bcjG7UV.png
<p align="center">
<img src="https://i.imgur.com/bcjG7UV.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>



<br />
<br />

<b>Part 5. Attack Map Creation</br>

Within Sentine, create a new Workbook

Delete the prepopulated elements and add a “Query” element

Go to the advanced editor tab, and paste the JSON

Workbook (Attack map):
map.json

Observe the query
Observe the map settings
Observe the map
https://i.imgur.com/mNyvFWl.png
<p align="center">
<img src="https://i.imgur.com/mNyvFWl.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>


Finished!




