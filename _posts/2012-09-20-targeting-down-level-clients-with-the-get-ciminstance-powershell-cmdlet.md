---
ID: 5407
post_title: >
  Targeting Down Level Clients with the
  Get-CimInstance PowerShell Cmdlet
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/09/20/targeting-down-level-clients-with-the-get-ciminstance-powershell-cmdlet/
published: true
post_date: 2012-09-20 07:30:19
---
This past weekend, I attended PowerShell Saturday 002 in Charlotte, NC. One of the sessions I attended was "Discovering the Power of WMI &amp; PowerShell in the Enterprise" by <a href="http://twitter.com/bwilhite1979" target="_blank">Brian Wilhite</a>.

One of the cool things that Brian covered in his session was how to use the new PowerShell version 3 <em>Get-CimInstance</em> cmdlet to target down level clients that don't have PowerShell installed or don't have PowerShell remoting enabled.

There's no parameter to change the protocol on the actual <em>Get-CimInstance</em> or <em>New-CimSession</em> cmdlets, but it can be added using the the <em>New-CimSessionOption</em> cmdlet.
<pre class="lang:ps decode:true">$Opt = New-CimSessionOption -Protocol Dcom
$CimSession = New-CimSession -ComputerName 'server2', 'server3' -SessionOption $Opt
$CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim0.jpg"><img class="alignnone size-full wp-image-5423" title="ps-cim0" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim0.jpg" width="605" height="212" /></a>

I'm able to query a 2003 R2 server that doesn't even have PowerShell installed and a 2008 R2 server that doesn't have PowerShell remoting enabled:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession $CimSession -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim2.jpg"><img class="alignnone size-full wp-image-5413" title="ps-cim2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim2.jpg" width="640" height="113" /></a>

I've been switching back to the <em>Get-WMIObject</em> cmdlet for down level clients, but then I have to use one command for one set of machines and potentially another for the machines that have PowerShell Remoting enabled if I want to take advantage of it. Brian's session got my wheels turning and I started thinking "What if I want to query machines that have PowerShell remoting enabled and down level clients via DCOM in the same command?"

I stored a couple of more CIM Sessions in a different variable using the default settings:
<pre class="lang:ps decode:true">$CimSession2 = New-CimSession -ComputerName 'www', 'vmhost'
$CimSession2</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim3.jpg"><img class="alignnone size-full wp-image-5414" title="ps-cim3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim3.jpg" width="599" height="200" /></a>

You can see all the sessions I have open and what protocol they're using with the <em>Get-CimSession</em> cmdlet:
<pre class="lang:ps decode:true">Get-CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim4.jpg"><img class="alignnone size-full wp-image-5415" title="ps-cim4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim4.jpg" width="379" height="344" /></a>

The only way I was able to use more than one variable when one or more of them is an array (contains more than one item) was in a loop. I used the <em>ForEach-Object</em> cmdlet in this example but that seemed to be more complicated than it needed to be:
<pre class="lang:ps decode:true">$CimSession, $CimSession2 |
ForEach-Object {Get-CimInstance -CimSession $_ -ClassName Win32_OperatingSystem -property csname, caption, version} |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim5.jpg"><img class="alignnone size-full wp-image-5416" title="ps-cim5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim5.jpg" width="593" height="163" /></a>

Let's just start over. Removing the sessions and verifying they're removed:
<pre class="lang:ps decode:true">Get-CimSession | Remove-CimSession
Get-CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim6.jpg"><img class="alignnone size-full wp-image-5417" title="ps-cim6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim6.jpg" width="420" height="55" /></a>

I've created the sessions similar to before except not placing them in a variable at the same time:
<pre class="lang:ps decode:true">New-CimSession -ComputerName 'server2', 'server3' -SessionOption $Opt
New-CimSession -ComputerName 'www', 'vmhost'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim7.jpg"><img class="alignnone size-full wp-image-5418" title="ps-cim7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim7.jpg" width="640" height="397" /></a>

You can see the sessions just like before by using the <em>Get-CimSession</em> cmdlet:
<pre class="lang:ps decode:true">Get-CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim8.jpg"><img class="alignnone size-full wp-image-5419" title="ps-cim8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim8.jpg" width="379" height="345" /></a>

I'll place all of them into one variable, both the DCOM and WSMAN ones:
<pre class="lang:ps decode:true">$CimSession = Get-CimSession
$CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim9.jpg"><img class="alignnone size-full wp-image-5420" title="ps-cim9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim9.jpg" width="381" height="371" /></a>

Now to run a simple one liner which query's all four clients, two with DCOM and two with WSMAN:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession $CimSession -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim10.jpg"><img class="alignnone size-full wp-image-5421" title="ps-cim10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim10.jpg" width="640" height="139" /></a>

Is that cool or what? No more switching back and forth between <em>Get-WMIObject</em> and <em>Get-CimInstance</em> for me.

After thinking about this for a while, my thought was "Why do I even need to store this in a variable?". I'll just put the <em>Get-CimSession</em> cmdlet in parenthesis:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession (Get-CimSession) -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim11.jpg"><img class="alignnone size-full wp-image-5457" title="ps-cim11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim11.jpg" width="640" height="130" /></a>

I took a look at the CimSession parameter of <em>Get-CimInstance</em> cmdlet to see if it accepts pipeline input. It does and the <em>Get-CimSession</em> cmdlet produces a CimSession Object for its output:
<pre class="lang:ps decode:true">help Get-CimInstance -Parameter CimSession
Get-CimSession | Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim14.jpg"><img class="alignnone size-full wp-image-5467" title="ps-cim14" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim14.jpg" width="481" height="269" /></a>

A lightbulb came on. I can pipe the output of <em>Get-CimSession</em> to <em>Get-CimInstance</em>:
<pre class="lang:ps decode:true">Get-CimSession |
Get-CimInstance -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim13.jpg"><img class="alignnone size-full wp-image-5459" title="ps-cim13" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim13.jpg" width="625" height="151" /></a>

I decided to take one last look at this. If I'm only going to use the variable for setting the <em>New-CimSessionOption</em> once, then I can use parenthesis and skip the variable. I double checked and <em>New-CimSessionOption</em> can't be piped to <em>New-CimSession</em>. An additional option I found while researching the different parameters is that the CIM sessions can be named as shown in this example where I've used group1 for the first set of servers and group2 for the second set of servers:
<pre class="lang:ps decode:true">New-CimSession -ComputerName 'server2', 'server3' -SessionOption (New-CimSessionOption -Protocol Dcom) -Name group1
New-CimSession -ComputerName 'www', 'vmhost' -Name group2</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim15.jpg"><img class="alignnone size-full wp-image-5470" title="ps-cim15" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim15.jpg" width="542" height="370" /></a>

This way I can easily run commands against all of the servers that I've named group# by specifying group*. This is helpful if other CIM sessions besides the ones named group# exist.
<pre class="lang:ps decode:true">Get-CimSession -Name group* |
Get-CimInstance -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim16.jpg"><img class="alignnone size-full wp-image-5471" title="ps-cim16" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim16.jpg" width="624" height="148" /></a>

Or I can run commands against each group of servers individually:
<pre class="lang:ps decode:true">Get-CimSession -Name group1 |
Get-CimInstance -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize

Get-CimSession -Name group2 |
Get-CimInstance -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim17.jpg"><img class="alignnone size-full wp-image-5472" title="ps-cim17" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim17.jpg" width="623" height="277" /></a>

If I have half a dozen different groups of servers defined as group1 through 6, I can pick and chose the different groups of servers as shown below instead of having to pick all the individual CIM sessions:
<pre class="lang:ps decode:true">Get-CimSession -Name group1, group2 |
Get-CimInstance -ClassName Win32_OperatingSystem -property csname, caption, version |
select csname, caption, version |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim18.jpg"><img class="alignnone size-full wp-image-5473" title="ps-cim18" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/ps-cim18.jpg" width="622" height="147" /></a>

For anyone who finds this interesting and wants to get their wheels turning as I did by thinking about what was covered in Brian Wilhite's session, he will be presenting another WMI &amp; PowerShell session at <a href="http://powershellsaturday.com/003/" target="_blank">PowerShell Saturday 003 in Atlanta</a> on October 27th titled "<a href="http://powershellsaturday.com/003/presentation/a-beginners-look-at-powershell-and-wmi-in-the-enterprise-environment/" target="_blank">A Beginners Look at PowerShell and WMI in the Enterprise Environment</a>".

µ