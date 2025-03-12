
<h1>SOC + SIEM (Monitoring Live Attacks)</h1>
    
<h2>Description</h2>
    <p>In this lab, I set up a basic home SOC in Azure from scratch. By creating a virtual machine (VM), opening it to the internet as a honeypot, forwarding logs to a central repository, and integrating Microsoft Sentinel, I was able to analyze real-world attack data.</p>
    
<h2>Part 1: Creating the Honeypot (Azure Virtual Machine)</h2>
    <p>Create a new Windows 10 virtual machine.</p>
    <img src="https://i.imgur.com/wgeLfAv.png" width="80%" alt="Creating VM">
    <p>Go to the Network Security Group for your virtual machine and create a rule that allows all traffic inbound.</p>
    <img src="https://i.imgur.com/1kVGmm9.png" width="80%" alt="Security Group Rule">
    <p>Log into your virtual machine and turn off the Windows firewall (Start -> wf.msc -> Properties -> All Off).</p>
    
<h2>Part 2: Logging into the VM and Inspecting Logs</h2>
    <p>Fail three login attempts as “employee” (or another username). Open Event Viewer and inspect security logs. Look for Event ID 4625, which records failed login attempts.</p>
    
<h2>Part 3: Log Forwarding and KQL</h2>
    <p>Create a Log Analytics Workspace.</p>
    <img src="https://i.imgur.com/djl6UN5.png" width="80%" alt="Log Analytics Workspace">
    <p>Create a Sentinel instance and connect it to Log Analytics.</p>
    <img src="https://i.imgur.com/IL5ZjOl.png" width="80%" alt="Sentinel Instance">
    <p>Configure the “Windows Security Events via AMA” connector.</p>
    <img src="https://i.imgur.com/pncMomq.png" width="80%" alt="AMA Connector">
    <p>Create the Data Collection Rule (DCR) within Sentinel.</p>
    <img src="https://i.imgur.com/B4KP98F.png" width="80%" alt="DCR Creation">
    <p>Query logs within the Log Analytics Workspace (LAW).</p>
    <img src="https://i.imgur.com/CXD8kDC.png" width="80%" alt="Query Logs">
    
<h2>Part 4: Log Enrichment and Finding Location Data</h2>
    <p>SecurityEvent logs in the Log Analytics Workspace contain only IP addresses. We import a spreadsheet (“Sentinel Watchlist”) with geographic information for IP addresses.</p>
    <p>Download: <a href="https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view?usp=sharing">geoip-summarized.csv</a></p>
    <p>Within Sentinel, create the watchlist:</p>
    <ul>
        <li>Name/Alias: geoip</li>
        <li>Source type: Local File</li>
        <li>Number of lines before row: 0</li>
        <li>Search Key: network</li>
    </ul>
    <img src="https://i.imgur.com/kP5aC6J.png" width="80%" alt="Watchlist Import">
    <p>Observe the logs now have geographic information to identify attack origins.</p>
    <img src="https://i.imgur.com/bcjG7UV.png" width="80%" alt="Geographic Log Data">
    
<h2>Part 5: Attack Map Creation</h2>
    <p>Within Sentinel, create a new Workbook. Delete prepopulated elements and add a “Query” element.</p>
    <p>Go to the advanced editor tab and type the <a href = "https://drive.google.com/file/d/1ErlVEK5cQjpGyOcu4T02xYy7F31dWuir/view">JSON query</a>.</p>
    <p>Observe the query, map settings, and final visualization.</p>
    <img src="https://i.imgur.com/mNyvFWl.png" width="80%" alt="Attack Map">
    
<h2>Conclusion</h2>
    <p>This lab provided valuable hands-on experience in monitoring real-world attacks using Microsoft Sentinel. By setting up a honeypot, forwarding logs, enriching data, and visualizing attacks, I gained practical skills in SOC operations and SIEM analysis.</p>
