---
ID: 12164
post_title: 'My Solution: July 2015 PowerShell Scripting Games Puzzle'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/08/06/my-solution-july-2015-powershell-scripting-games-puzzle/
published: true
post_date: 2015-08-06 07:30:07
---
Last month, <a href="http://powershell.org/" target="_blank">PowerShell.org</a> announced that the PowerShell Scripting Games had been re-imagined as a monthly puzzle and the first puzzle was published.

The instructions stated to use a PowerShell one-liner that produces the following output. No more than one semicolon should be used, do not use the <em>ForEach-Object</em> cmdlet or one of its aliases. The one-liner should be able to target more than one computer and feel free to go crazy with a really short one-liner with aliases and whatever else.
<pre class="lang:ps decode:true">PSComputerName ServicePackMajorVersion Version  BIOSSerial</pre>
Although the instructions state that one semicolon can be used, this does not mean that two PowerShell commands separated by a semicolon could be used because that wouldn't be a one-liner. A PowerShell one-liner is one continuous pipeline and not necessarily on one physical line.

Here's my solution with full cmdlet and parameter names so you'll be able to understand it easier. I don't recommend using aliases or positional parameters in any code that you're sharing even if it's a one-liner. The use of full cmdlet and parameter names makes your code much easier to follow and more self-documenting.
<pre class="lang:ps decode:true">Get-WmiObject -ComputerName $env:COMPUTERNAME -Class Win32_OperatingSystem -PipelineVariable OSInfo |
Select-Object -Property PSComputerName, ServicePackMajorVersion, Version,
@{label='BIOSSerial';expression={(Get-WmiObject -ComputerName $OSInfo.CSName -Class Win32_BIOS).serialnumber}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle1a.jpg"><img class="alignnone size-full wp-image-12165" src="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle1a.jpg" alt="July2015-SgPuzzle1a" width="877" height="167" /></a>

My solution does require PowerShell version 4 or higher since I'm taking advantage of the <em>PipelineVariable</em> common parameter.

You may wonder why I'm using the older <em>Get-WmiObject</em> cmdlet instead of the <em>Get-CimInstance</em> cmdlet that was introduced in PowerShell version 3 since I'm using features that require PowerShell version 4?

If you're using a client machine such as Windows 8.1 or Windows 10 with its default setting of not having PowerShell remoting enabled, the <em>Get-CimInstance</em> cmdlet will fail when the <em>ComputerName</em> parameter is specified and the local computer is targeted:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle4a.jpg"><img class="alignnone size-full wp-image-12176" src="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle4a.jpg" alt="July2015-SgPuzzle4a" width="877" height="202" /></a>

This is because <em>Get-CimInstance</em> uses the WSMAN protocol by default, although a CimSession could be created specifying the DCOM protocol with the <em>New-CimSessionOption</em> cmdlet or PowerShell remoting could be enabled on the local computer.

You may also run into similar issues with remote machines when using the <em>Get-WmiObject</em> cmdlet which communicates via DCOM which may be blocked on your network or via the firewall on remote machines.

My solution also works without issue against multiple computers and notice that although this command is on more than one physical line, it is indeed a PowerShell one-liner:
<pre class="lang:ps decode:true ">Get-WmiObject -ComputerName $env:COMPUTERNAME, DC01, SQL04 -Class Win32_OperatingSystem -PipelineVariable OSInfo |
Select-Object -Property PSComputerName, ServicePackMajorVersion, Version,
@{label='BIOSSerial';expression={(Get-WmiObject -ComputerName $OSInfo.CSName -Class Win32_BIOS).serialnumber}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle2a.jpg"><img class="alignnone size-full wp-image-12166" src="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle2a.jpg" alt="July2015-SgPuzzle2a" width="877" height="203" /></a>

I decided to take my solution one step further. What if no semicolons were allowed? I also saw someone had posted that their computer didn't have a "BIOSSerial" property. No one's computer has one. You simply create a hash table with <em>Select-Object</em> or one of the <em>Format</em> cmdlets and rename an existing property. Here's my solution with no semicolons:
<pre class="lang:ps decode:true ">Get-WmiObject -ComputerName $env:COMPUTERNAME, DC01, SQL04 -Class Win32_OperatingSystem -PipelineVariable OSInfo |
Select-Object -Property PSComputerName, ServicePackMajorVersion, Version,
@{
    label='BIOSSerial'
    expression={(Get-WmiObject -ComputerName $OSInfo.CSName -Class Win32_BIOS).serialnumber}
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle5a.jpg"><img class="alignnone size-full wp-image-12181" src="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle5a.jpg" alt="July2015-SgPuzzle5a" width="877" height="239" /></a>

I did carve up the command and use all of the aliases and shortcuts I could think of and I'll post it here and let you be the judge of whether or not this command or the previous one is easier to follow:
<pre class="lang:ps decode:true ">gwmi Win32_OperatingSystem -cn. -pv c|ft PSC*,S*P*Ma*,V*,@{l='BIOSSerial';e={(gwmi Win32_BIOS -cn $c.CSName).serialnumber}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle3a.jpg"><img class="alignnone size-full wp-image-12167" src="http://mikefrobbins.com/wp-content/uploads/2015/08/July2015-SgPuzzle3a.jpg" alt="July2015-SgPuzzle3a" width="877" height="144" /></a>

There's nothing wrong with using shortcuts such as aliases and positional parameters when working in the console as long as the code isn't being saved or shared. It's also beneficial to know what the aliases are since you'll find others posting code that you'll need to decrypt.

To learn more about this scripting games puzzle, see the "<a href="http://powershell.org/wp/2015/07/04/2015-july-scripting-games-puzzle/" target="_blank">2015-July Scripting Games Puzzle</a>" article on PowerShell.org.

µ