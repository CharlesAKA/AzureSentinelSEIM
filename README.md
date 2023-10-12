<h1>Using Azure Sentinel (Cloud based VM)</h1> 
 
<h2>✍🏿Description</h2>
Created a honeypot VM in the cloud and we are going to monitor and record attacks that are coming from different IP addresses from different countries all over the world. Then I will take the data and display in on a map to show the location of the incoming attacks.
Inspected already failed IDP logs and put them on the lab.


<br />


<h2>🧑🏿‍💻Environments Used </h2>

- <b>Azure Sentinel</b>
- <b>Remote Desktop Protocol</b>

<h2>🚶🏿Walk-through:</h2>
<h3>Part1️⃣ :</h3>

<p align="center">
 
- <b>Create a Virtual Machine in Microdoft Azure</b>
- <b>This will be the honeypot machine connected to the internet where all the different people from different lacation will brute force attack</b>

 <br/>
<img src="https://imgur.com/A7Up3mO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />


<br />
 <h3>Part2️⃣ :</h3>
 <p align="center">
   
  - <b>Now we are going to make our log analytic workspace to ingest logs from the VM.</b>
  - <b>We are going to ingest the windows event logs and create our own custom logs that contains geographical information. This is where the logs will be stored and our SIEM address sentinel will connect to the workspace to be able to display the geodata on the map.</b>
  
  </b>
 <br/>
<img src="https://imgur.com/IdIgQUF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

 - <b>Next I will set up sentinel in azure, this will be the SIEM to visualize the attacks</b>
 - <b>After logging in the VM, I will go on Event Viewer-> windows logs-> security and I will be able to see the security events and notice the event ID 4625. This shoes the audit failure which are the failed log in attempts, this displays informations such as the username used when they were trying to log in and the target machine and the failure reason, your workstation and IP address.</b>
 

<img src="https://imgur.com/YQJyh2O.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

- <b>I will be gathering all the failures of people trying to log in and take the IP address and then use the API from ipgeolocation.com and post this IP address. The API will return the country it came from, the state, the longitude and latitude. I will use this to create my own custom log and I will send that log to log analytics workspace in azure and use the sentinel to read the information and plot it out on a map.</b>
<br />
- <b>With PowerShell we are going to take the IP address and use the IP geolocation API from ipgeolocation.io and use the information that it posts to create our own custom log and then send it to log analytics workspace in azure and then use the sentinel SIEM to read the latitude and longitude, country info and plot out where the different attacks came from.</b>
<img src="https://imgur.com/368kSJ2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

- <b>Run the PowerShell script and it showed my deliberate failed log in attempts as trial</b>
- <b>What happens is that it looks through the event viewer for the failed 4625 log ons</b>
- <b>Takes them out and sends them to the API IP address and gets the country in geodata and then creates a failed logon log file </b>

<br />
<img src="https://imgur.com/LjT9TqG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- <b>The firs few lines are sample data and the rest are actual failed logon attempts
In the query the sample will be excluded</b>
- <b>Now we have our custom log and script running</b>
- <b>Now we are going to create another custom log inside of our log analytics workspace that will allow us to bring the custom log with the geodata into our log analytics workspace</b>

<br />
<img src="https://imgur.com/LjT9TqG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

- <b>I then created a custom log in Azure inside of the log analytics workspace which will allow me to bring the custom log with the geodata into the log analytics workspace</b>
- <b>This will be used to train log analytics to find the log files that we are looking for in the log files</b>
- <b>The security event query shows the windows event log in formation brought into log analytics workspace, this query will return all of the failed RDP event logs, as you can see there are two events where I purposely failed logging in to display the result</b>

<img src="https://imgur.com/LjT9TqG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</b>
<br />
- <b>In this query you can see the custom log entries. I used the RawData and extracted the data to create its own different fields for Latitude, Longitude , DestinationHost , Username , Sourcehost , State , Country , Label , Timestamp.</b>
- <b>The next time someone fails to log in the data will be parsed out on the fields created </b>

<img src="https://imgur.com/06zfEG5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
- <b>Back in sentinel I set up the map with geolocation using the workbook feature and added a query to parse the data logs in. .</b>
- <b>This query processes the data related to the failed RDP connections with associated geographical information.  </b>
- <b>This will extract specific fields like latitude, longitude, destination host, username, etc., and then selects these fields for further analysis or presentation.</b>
<img src="https://imgur.com/k59o4fW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
- <b>Here you can see there are some subsequent login failures they will be shown on PowerShell.</b>
- <b>The failed log ins will go to windows event log and then go to the custom log for the extraction of the geo data</b>
<img src="https://imgur.com/vRoyz0O.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/vRoyz0O.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
