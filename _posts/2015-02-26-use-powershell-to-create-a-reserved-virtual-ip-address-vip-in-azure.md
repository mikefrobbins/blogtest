---
ID: 11247
post_title: >
  Use PowerShell to Create a Reserved
  Virtual IP Address (VIP) in Azure
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/02/26/use-powershell-to-create-a-reserved-virtual-ip-address-vip-in-azure/
published: true
post_date: 2015-02-26 07:30:59
---
By default, the VM's that you create in Azure will have a dynamic virtual IP address (VIP). Based on <a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-custom-domain-name/#add-cname" target="_blank">this article on Azure</a>, you could simply create a DNS CNAME record for your custom domain and point it to the DNS name that you chose during the creation of your azure VM which should prevent any problems if the virtual IP address happens to change.

Maybe you want a reserved virtual IP address for your Azure instance though? There's a limited number of reserved virtual IP addresses per subscription so make sure you use them sparingly if you do indeed want to create and use reserved virtual IP addresses.
<pre class="lang:ps decode:true">New-AzureReservedIP –ReservedIPName 'mikefrobbinsIP' –Location 'South Central US'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip1a.jpg"><img class="alignnone size-full wp-image-11248" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip1a.jpg" alt="azure-ip1a" width="877" height="130" /></a>

The <em>Get-AzureReservedIP</em> PowerShell cmdlet can be used to see the actual IP address once the reservation has been created. For security reasons, I've blurred out the actual IP address that was created in the following example:
<pre class="lang:ps decode:true">Get-AzureReservedIP –ReservedIPName 'mikefrobbinsIP'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip2a.jpg"><img class="alignnone size-full wp-image-11249" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip2a.jpg" alt="azure-ip2a" width="877" height="263" /></a>

The same cmdlet can be used to determine how many virtual IP addresses you've already reserved in Azure:
<pre class="lang:ps decode:true ">(Get-AzureReservedIP).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip3a.jpg"><img class="alignnone size-full wp-image-11270" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip3a.jpg" alt="azure-ip3a" width="877" height="73" /></a>

One of the catches is that you can't assign a reserved virtual IP address to a VM that already exists in Azure. Based on what I've read this is something that they're working on so it is subject to change.

The <em>Remove-AzureReservedIP</em> cmdlet is used to remove a reserved virtual IP address. If it's currently in use (associated with an Azure cloud service), the removal will fail:
<pre class="lang:ps decode:true ">Get-AzureReservedIP -ReservedIPName mikefrobbinsIP | Remove-AzureReservedIP</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip4a.jpg"><img class="alignnone size-full wp-image-11277" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-ip4a.jpg" alt="azure-ip4a" width="877" height="203" /></a>
<h5><span style="color: #ff0000;">Remove-AzureReservedIP : BadRequest: The Reserved IP mikefrobbinsIP is currently in use by Deployment</span>
<span style="color: #ff0000;">mikefrobbins-test belonging to HostedService mikefrobbins-test.</span>
<span style="color: #ff0000;">At line:1 char:54</span>
<span style="color: #ff0000;">+ ... ureReservedIP -ReservedIPName mikefrobbinsIP | Remove-AzureReservedIP</span>
<span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~</span>
<span style="color: #ff0000;"> + CategoryInfo : CloseError: (:) [Remove-AzureReservedIP], CloudException</span>
<span style="color: #ff0000;"> + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ServiceManagement.IaaS.RemoveAzureReservedIPCmdlet</span></h5>
Based on my testing, even if you delete the VM and the Cloud Service that the reserved virtual IP address is associated with, the IP will remain reserved and associated with your Azure subscription.

Recommended reading on MSDN: <a href="http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx" target="_blank">VIPs, DIPs and PIPs in Microsoft Azure</a> and <a href="https://msdn.microsoft.com/en-us/library/azure/dn690120.aspx" target="_blank">Reserved IP Addresses</a>.

µ