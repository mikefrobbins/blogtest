---
ID: 14791
post_title: 'No PowerShell Cmdlet to Start or Stop Something? Don&#8217;t Forget to Check for Methods on the Get Cmdlets'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/12/15/no-powershell-cmdlet-to-start-or-stop-something-dont-forget-to-check-for-methods-on-the-get-cmdlets/
published: true
post_date: 2016-12-15 07:30:57
---
Many PowerShell commands return output in the form of objects (some return nothing at all). An example of this is shown in the following example where properties and their corresponding values are returned.

CommandType is a property and Cmdlet is the value for that particular property for each of the results:
<pre class="lang:ps decode:true">Get-Command -Noun Service -Module Microsoft.PowerShell.Management</pre>
<img class="alignnone size-full wp-image-14792" src="http://mikefrobbins.com/wp-content/uploads/2016/12/methods1a.png" alt="methods1a" width="859" height="212" />

Keep in mind that what is shown in the default output may not be the actual property names (compare the default output from <em>Get-Process</em> to the properties listed from <em>Get-Process | Get-Member</em> for an example of this).

In addition to properties, commands may also have methods which are actions that can be performed. The <em>Get-Member</em> cmdlet is used to determine what properties (and their actual names) and methods a command has. Any command that produces output can be piped to <em>Get-Member</em>.
<pre class="lang:ps decode:true ">Get-Service -Name w32time | Get-Member</pre>
<img class="alignnone size-full wp-image-14793" src="http://mikefrobbins.com/wp-content/uploads/2016/12/methods2a.png" alt="methods2a" width="859" height="548" />

Notice as shown in the previous set of results that <em>Get-Service</em> has a start and stop method. I like to point this out because just because someone is only running commands that start with Get-* doesn't mean that they can't perform a RGE (resume generating event).

There are cmdlets for starting and stopping services and I recommend using them instead of the methods any time a specific command exist for performing that task, but if those two commands didn't exist the methods could be used:

The windows time service is currently running:
<pre class="lang:ps decode:true">Get-Service -Name w32time</pre>
<img class="alignnone size-full wp-image-14795" src="http://mikefrobbins.com/wp-content/uploads/2016/12/methods3a.png" alt="methods3a" width="859" height="127" />

Stop the service using <em>Get-Service</em> with the stop method:
<pre class="lang:ps decode:true">(Get-Service -Name w32time).Stop()
Get-Service -Name w32time</pre>
<img class="alignnone size-full wp-image-14796" src="http://mikefrobbins.com/wp-content/uploads/2016/12/methods4a.png" alt="methods4a" width="859" height="139" />

Start the service using <em>Get-Service</em> with the start method:
<pre class="lang:ps decode:true">(Get-Service -Name w32time).Start()
Get-Service -Name w32time</pre>
<img class="alignnone size-full wp-image-14797" src="http://mikefrobbins.com/wp-content/uploads/2016/12/methods5a.png" alt="methods5a" width="859" height="140" />

You might ask, how is this beneficial in the real world for the everyday average IT Pro?

Sometimes with a fictitious example like with <em>Get-Service</em>, it's hard to imagine <em>Start-Service</em> and <em>Stop-Service</em> not existing, but that's the exact scenario with the <em>Get-SqlAgentJob</em> cmdlet that's part of the SQLServer PowerShell module that installs as part of <a href="https://msdn.microsoft.com/en-us/library/mt238290.aspx" target="_blank">SQL Server Management Studio 2016</a>.

Notice that there's no <em>Start-SqlAgentJob</em> cmdlet:
<pre class="lang:ps decode:true ">Get-Command -Module SQLServer -Name *job*</pre>
<img class="alignnone size-full wp-image-14720" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob1a.png" alt="startsqljob1a" width="859" height="165" />

<em>Get-SqlAgentJob </em>returns a SMO object which has a start (and stop) method (thanks to <a href="https://twitter.com/sqldbawithbeard" target="_blank">Rob Sewell</a> for pointing this out):
<pre class="lang:ps decode:true">Get-SqlAgentJob -ServerInstance SQL011 -Name test | Get-Member -MemberType Method</pre>
<img class="alignnone size-full wp-image-14769" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob4a.png" alt="startsqljob4a" width="859" height="634" />

That means it can be used to start a SQL agent job even though a specific cmdlet for performing that task doesn't exist:
<pre class="lang:ps decode:true ">(Get-SqlAgentJob -ServerInstance SQL011 -Name test).Start()
Get-SqlAgentJob -ServerInstance SQL011 -Name test</pre>
<img class="alignnone size-full wp-image-14770" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob3a.png" alt="startsqljob3a" width="859" height="144" />

If I were asked in an interview what's the most important PowerShell command and I could only pick one, it would be <em>Get-Member</em> &lt;period&gt;.

µ