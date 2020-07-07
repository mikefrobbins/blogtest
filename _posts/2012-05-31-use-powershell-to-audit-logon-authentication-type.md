---
ID: 4300
post_title: >
  Use PowerShell to Audit Logon
  Authentication Type
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/31/use-powershell-to-audit-logon-authentication-type/
published: true
post_date: 2012-05-31 07:30:17
---
Want to know what type of authentication mechanism is being used when users log onto your servers? This script pulls the information from the event logs to determine how users are being authenticated. It uses Get-Winevent with the -FilterXPath parameter. That parameter and what the logon type numeric codes translate to are a couple of things that I haven't seen much documentation on. The script sorts by server name in ascending order and then by the time in descending order.
<pre class="lang:ps decode:true">&lt;#
.SYNOPSIS
Verify-Kerberos
.DESCRIPTION
Verify-Kerberos is used to pull the logon events from the event log of specific servers to determine what type of authentication mechanism is being used. Examples are NTLM and Kerberos.
.PARAMETER ComputerName
Specify remote server names to check. Default: The Local Computer
.PARAMETER Records
Specify the maximum number of events to be retrieved from each computer. Default: 10
.EXAMPLE
.\Verify-Kerberos.ps1 -ComputerName server1 | Format-Table -AutoSize
Retrieve 10 logon events from server1 and display them on the screen in a table.
.EXAMPLE
.\Verify-Kerberos.ps1 -ComputerName server1, server2 -Records 30 | Export-Csv -NoTypeInformation -Path d:\tmp\voyager-kerberos_test.csv
Retrieve 30 logon events from server1 and 30 from server2. Save the results as a CSV file located in the specified path.
.Notes
LastModified: 5/30/2012
Author: Mike F Robbins
#&gt;
param (
$ComputerName = $Env:ComputerName,
$Records = 10
)
function Get-LogonTypeName {
Param($LogonTypeNumber)
switch ($LogonTypeNumber) {
0 {"System"; break;}
2 {"Interactive"; break;}
3 {"Network"; break;}
4 {"Batch"; break;}
5 {"Service"; break;}
6 {"Proxy"; break;}
7 {"Unlock"; break;}
8 {"NetworkCleartext"; break;}
9 {"NewCredentials"; break;}
10 {"RemoteInteractive"; break;}
11 {"CachedInteractive"; break;}
12 {"CachedRemoteInteractive"; break;}
13 {"CachedUnlock"; break;}
default {"Unknown"; break;}
}
}
$ComputerName | ForEach-Object {Get-Winevent -Computer $_ -MaxEvents $Records -FilterXPath "*[System[(EventID=4624)]]" |
select @{Name='Time';e={$_.TimeCreated.ToString('g')}},
@{l="Logon Type";e={Get-LogonTypeName $_.Properties[8].Value}},
@{l='Authentication';e={$_.Properties[10].Value}},
@{l='User Name';e={$_.Properties[5].Value}},
@{l='Client Name';e={$_.Properties[11].Value}},
@{l='Client Address';e={$_.Properties[18].Value}},
@{l='Server Name';e={$_.MachineName}}} |
Sort-Object @{e="Server Name";Descending=$false}, @{e="Time";Descending=$true}</pre>
I've trimmed part of the time and server name columns off the sides of the image below to make it display properly on this blog. Click on the images to display them completely.
<pre class="lang:ps decode:true">.\Verify-Kerberos.ps1 -ComputerName mail, web1, vmhost | ft -auto</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb1.png"><img class="alignnone size-full wp-image-4322" alt="v-kerb1" src="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb1.png" width="628" height="478" /></a>

By default, most of this information is returned as part of the "Message" property and it doesn't appear that individual items can be retrieved from it:
<pre class="lang:ps decode:true">Get-Winevent -MaxEvents 1 -FilterXPath "*[System[(EventID=4624)]]" | fl</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb2.png"><img class="alignnone size-full wp-image-4327" alt="v-kerb2" src="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb2.png" width="558" height="534" /></a>

The "Properties" collection allows access to the individual values. Here's how I determined what position the properties I wanted to use were in:
<pre class="lang:ps decode:true">(Get-Winevent -MaxEvents 1 -FilterXPath "*[System[(EventID=4624)]]").Properties</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb3.png"><img class="alignnone size-full wp-image-4326" alt="v-kerb3" src="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb3.png" width="549" height="337" /></a>

As you can see, the values in the collection shown in the image above line up with what the script retrieves which is shown in the image below:
<pre class="lang:ps decode:true">.\Verify-Kerberos.ps1 -Records 1 | ft -auto</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb4.png"><img class="alignnone size-full wp-image-4328" alt="v-kerb4" src="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb4.png" width="581" height="74" /></a>

To determine what value should be used with the -FilterXPath parameter, I searched the event logs for Event ID 4624 and used the information from the XML View tab as shown in the image below:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb5.png"><img class="alignnone size-full wp-image-4347" title="v-kerb5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/v-kerb5.png" width="632" height="440" /></a>

Want a copy of this script? <a href="http://gallery.technet.microsoft.com/Audit-Logon-Authentication-ffc27095" target="_blank">Download it</a> from the Microsoft TechNet Script Repository.

µ