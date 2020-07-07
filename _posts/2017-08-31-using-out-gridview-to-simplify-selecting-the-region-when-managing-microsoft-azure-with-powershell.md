---
ID: 15552
post_title: >
  Using Out-GridView to simplify selecting
  the region when managing Microsoft Azure
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/08/31/using-out-gridview-to-simplify-selecting-the-region-when-managing-microsoft-azure-with-powershell/
published: true
post_date: 2017-08-31 07:30:10
---
You've <a href="https://azure.microsoft.com/en-us/free/" target="_blank" rel="noopener">signed up for a Microsoft Azure account</a> and you've installed the Azure Resource Manager PowerShell cmdlets on your computer.
<pre class="lang:ps decode:true">Install-Module -Name AzureRM -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region1a.jpg"><img class="alignnone size-full wp-image-15553" src="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region1a.jpg" alt="" width="859" height="171" /></a>

You login to Azure from PowerShell. You'll normally see most people use <em>Login-AzureRmAccount</em>, but that command is an alias (Login isn't an approved verb).
<pre class="lang:ps decode:true ">Get-Alias -Definition Add-AzureRmAccount</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region2a.jpg"><img class="alignnone size-full wp-image-15554" src="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region2a.jpg" alt="" width="859" height="131" /></a>

Login to Azure and provide the account login information when prompted:
<pre class="lang:ps decode:true ">Add-AzureRmAccount</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region3a.jpg"><img class="alignnone size-full wp-image-15555" src="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region3a.jpg" alt="" width="859" height="485" /></a>

Several of the cmdlets in the Azure Resource Manager PowerShell module require a location (a region) to be specified when creating things. To me, showing the regions in the PowerShell console, figuring out which region I want, and then storing it in a variable is a bit haphazard.
<pre class="lang:ps decode:true ">Get-AzureRmLocation</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region4a.jpg"><img class="alignnone size-full wp-image-15556" src="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region4a.jpg" alt="" width="859" height="445" /></a>

I've found that using <em>Out-GridView</em> makes the process simpler:
<pre class="lang:ps decode:true">$Region = (
    Get-AzureRmLocation |
    Sort-Object -Property Location | 
    Select-Object -Property Location, Displayname |
    Out-GridView -OutputMode Single -Title 'Select an Azure Region'
).Location</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region5a.jpg"><img class="alignnone size-full wp-image-15557" src="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region5a.jpg" alt="" width="859" height="584" /></a>

Now the region that I selected is stored in a variable that I can use when creating items in Azure:
<pre class="lang:ps decode:true ">$Region</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region6a.jpg"><img class="alignnone size-full wp-image-15558" src="http://mikefrobbins.com/wp-content/uploads/2017/08/azure-region6a.jpg" alt="" width="859" height="70" /></a>

Although the <em>Out-GridView</em> cmdlet existed in PowerShell version 2.0, you'll need PowerShell version 3.0 to use it as shown in this blog article. The <em>OutputMode</em> parameter of <em>Out-GridView</em> which is used in this blog article was added in PowerShell version 3.0. Also, <em>Out-GridView</em> can only be used on operating systems with a GUI (it cannot be used on server core).

As far as I know, there's no way to set a default region in Azure like there is with the AWS <em>Initialize-AWSDefaultConfiguration</em> cmdlet. I guess if you really wanted to set a default, you could always use <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_parameters_default_values?view=powershell-5.1" target="_blank" rel="noopener">$PSDefaultParameterValues</a> to set a default value for the <em>Location</em> parameter for the cmdlets that require it.

µ