---
ID: 12467
post_title: 'PowerShell: Filter by User when Querying the Security Event Log with Get-WinEvent and the FilterHashTable Parameter'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/10/01/powershell-filter-by-user-when-querying-the-security-event-log-with-get-winevent-and-the-filterhashtable-parameter/
published: true
post_date: 2015-10-01 07:30:28
---
I recently ran across something interesting that I thought I would share. The help for the <em>FilterHashTable</em> parameter of <em>Get-WinEvent</em> says that you can filter by UserID using an Active Directory user account's SID or domain account name:
<pre class="lang:ps decode:true ">help Get-WinEvent -Parameter filterhashtable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data1a.jpg"><img class="alignnone size-full wp-image-12469" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data1a.jpg" alt="filterhashtable-data1a" width="877" height="799" /></a>

Notice that the help also says <span style="color: #ff0000;">the data key can be used for unnamed fields in classic event logs</span>. I often hear the question wanting to know what the valid key pairs are for the hash table. As you can see, they're listed in the help.

First, we'll start out by determining which domain controller in our Active Directory domain holds the PDC emulator FSMO role since information for all account lockouts that occur in a domain are stored in the security event log of the PDC emulator. Don't over-complicate locating the PDC emulator. If you have the Active Directory PowerShell module installed which installs as part of RSAT (Remote Server Administration Tools), PDCEmulator is one of the properties that is returned by default by the <em>Get-ADDomain</em> cmdlet:
<pre class="lang:ps decode:true ">Get-ADDomain | Select-Object -Property pdcemulator</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data8a.jpg"><img class="alignnone size-full wp-image-12493" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data8a.jpg" alt="filterhashtable-data8a" width="877" height="132" /></a>

Now, we'll query the security event log on the PDC emulator for all account lockout events:
<pre class="lang:ps decode:true">Get-WinEvent -ComputerName dc01 -FilterHashtable @{logname='security';id=4740}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data2a.jpg"><img class="alignnone size-full wp-image-12470" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data2a.jpg" alt="filterhashtable-data2a" width="877" height="203" /></a>

We're looking for lockout events for a user with the userid of 'afuller' so let's grab the SID for his user account:
<pre class="lang:ps decode:true">Get-ADUser -Identity afuller</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data3a.jpg"><img class="alignnone size-full wp-image-12471" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data3a.jpg" alt="filterhashtable-data3a" width="877" height="238" /></a>

As the help stated, we'll add the userid key and the user's SID to our hash table:
<pre class="lang:ps decode:true ">Get-WinEvent -ComputerName dc01 -FilterHashtable @{logname='security';id=4740;userid='S-1-5-21-3309960685-2715817658-858357121-1407'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data4a.jpg"><img class="alignnone size-full wp-image-12472" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data4a.jpg" alt="filterhashtable-data4a" width="877" height="154" /></a>

As shown in the previous set of results, a message is received stating no events exist that match the specified criteria.

Usually, this is where most people will simply pipe to <em>Where-Object</em> because they can't figure out how to filter left by user. The UserID key doesn't work as expected in this scenario, so an alternate method is to use the data key in the hash table instead of the userid key and specify the user's SID as the value:
<pre class="lang:ps decode:true ">Get-WinEvent -ComputerName dc01 -FilterHashtable @{logname='security';id=4740;data='S-1-5-21-3309960685-2715817658-858357121-1407'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data5a.jpg"><img class="alignnone size-full wp-image-12473" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data5a.jpg" alt="filterhashtable-data5a" width="877" height="191" /></a>

You can also use the data key to filter by userid:
<pre class="lang:ps decode:true ">Get-WinEvent -ComputerName dc01 -FilterHashtable @{logname='security';id=4740;data='afuller'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data6a.jpg"><img class="alignnone size-full wp-image-12474" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data6a.jpg" alt="filterhashtable-data6a" width="877" height="179" /></a>

Now we can add a couple of custom properties to determine what device is causing the account lockout:
<pre class="lang:ps decode:true ">Get-WinEvent -ComputerName dc01 -FilterHashtable @{logname='security';id=4740;data='afuller'} |
Select-Object -Property timecreated,
@{label='username';expression={$_.properties[0].value}},
@{label='computername';expression={$_.properties[1].value}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data7a.jpg"><img class="alignnone size-full wp-image-12475" src="http://mikefrobbins.com/wp-content/uploads/2015/09/filterhashtable-data7a.jpg" alt="filterhashtable-data7a" width="877" height="191" /></a>

The moral of the story here is there are hidden gems in the built in help so don't underestimate its content.

µ