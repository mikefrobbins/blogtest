---
ID: 5278
post_title: >
  Finding Aliases for PowerShell Cmdlet
  Parameters
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/30/finding-aliases-for-powershell-cmdlet-parameters/
published: true
post_date: 2012-08-30 07:30:20
---
Many PowerShell cmdlet parameters have aliases such as "Cn" for ComputerName. These aliases aren't well documented and even viewing the full help for a cmdlet doesn't show them. The following command can be used to display a list of all the parameters for a particular PowerShell cmdlet and any aliases that exist for those parameters:
<pre class="lang:ps decode:true">(Get-Command (Read-Host "Enter a PowerShell Cmdlet Name")).parameters.values |
select name, aliases</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases1.jpg"><img class="alignnone size-full wp-image-5279" title="parameter-aliases1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases1.jpg" width="640" height="227" /></a>

One thing to keep in mind is this is a list of all the parameters from all parameter sets which means some of them are mutually exclusive and can't be used with one another.

If you only want to display the parameters that have aliases, add the <em>Where-Object</em> cmdlet to the command and test to see if aliases is true:
<pre class="lang:ps decode:true">(Get-Command (Read-Host "Enter a PowerShell Cmdlet Name")).parameters.values |
where aliases | select name, aliases</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases2.jpg"><img class="alignnone size-full wp-image-5280" title="parameter-aliases2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases2.jpg" width="640" height="206" /></a>

Here's a parameterized script with a mandatory parameter for the cmdlet name and an optional parameter for the parameter name:
<pre class="lang:ps decode:true">param (
[Parameter(Mandatory=$True)]
[string]$CmdletName,
[string]$ParameterName = '*'
)
(Get-Command $CmdletName).parameters.values |
where name -like $ParameterName | select name, aliases</pre>
Running the script without at least specifying the mandatory parameter will prompt for it. Specifying only a cmdlet name returns all parameters and any aliases that exist for them:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases3.jpg"><img class="alignnone size-full wp-image-5282" title="parameter-aliases3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases3.jpg" width="533" height="275" /></a>

Specifying the cmdlet and parameter name only returns that particular parameter and the aliases for it if any exist:<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases4.jpg"><img class="alignnone size-full wp-image-5283" title="parameter-aliases4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases4.jpg" width="640" height="61" /></a>

<span style="text-decoration: underline;">Update 08/30/12</span>
The full help in PowerShell version 3 does show parameter aliases. The aliases for a specific parameter can be viewed using <em>Help CmdletName -Parameter ParameterName</em>:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases5.jpg"><img class="alignnone size-full wp-image-5291" title="parameter-aliases5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/parameter-aliases5.jpg" width="460" height="132" /></a>

µ