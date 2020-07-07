---
ID: 11223
post_title: >
  Use PowerShell to create a Storage
  Account in Azure
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/02/19/use-powershell-to-create-a-storage-account-in-azure/
published: true
post_date: 2015-02-19 07:30:10
---
You’ll need to sign up for an Azure account if you don’t already have one: <a href="http://azure.microsoft.com/en-us/">http://azure.microsoft.com</a>. There’s a free trial if you want to try it out.

One thing I would suggest if you've never used Azure is to spend a little time in the GUI (your Azure account's portal website) learning about it before you start trying to manage it with PowerShell. That's the same advice I would give to anyone wanting to do something with PowerShell. For example, if you want to create an Active Directory user with PowerShell and you've never created one in the GUI. Start with the GUI, learn the process, and then move to PowerShell which will give you a better understanding of what you're trying to accomplish.

There's a Microsoft article on “<a href="http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/" target="_blank">How to install and configure Azure PowerShell</a>” that will walk you through the process of installing the Azure PowerShell module so there's no reason to duplicate that information here.

<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage7a.jpg"><img class="alignnone size-full wp-image-11232" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage7a.jpg" alt="azure-storage7a" width="746" height="385" /></a>

At this point, you should already have your Azure account connected as shown in that article by using the <em>Add-AzureAccount</em> PowerShell cmdlet.
<pre class="lang:ps decode:true  ">Add-AzureAccount</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage8a.jpg"><img class="alignnone size-full wp-image-11233" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage8a.jpg" alt="azure-storage8a" width="877" height="136" /></a>

One of the first things that you'll need to do is to setup a storage account. This is necessary before you can start creating VM's in Azure with PowerShell, but first you'll need to decide what tier and instance type of VM that you want to create so that you can create the storage account in an Azure datacenter that supports your desired VM type. The virtual machine tier and instance type information can be found <a href="http://azure.microsoft.com/en-us/pricing/details/virtual-machines/" target="_blank">here</a>.

Based on that information, I want to create a basic tier instance type A1. I also want to use one of Azure's datacenters that exists in the United States so based on that information, I'll use PowerShell to determine which Azure locations support my desired configuration:
<pre class="lang:ps decode:true ">Get-AzureLocation |
Where-Object {$_.Name -like '*US*' -and $_.VirtualMachineRoleSizes -contains 'Basic_A1'} |
Select-Object -Property Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage1a.jpg"><img class="alignnone size-full wp-image-11225" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage1a.jpg" alt="azure-storage1a" width="877" height="216" /></a>

There are certain restrictions on what characters the storage account name can contain. See this article on "<a href="http://blogs.msdn.com/b/jmstall/archive/2014/06/12/azure-storage-naming-rules.aspx" target="_blank">Azure Storage Naming Rules</a>" for details. You'll receive an error if you try adding something like a dash to it as shown in the following example:
<pre class="lang:ps decode:true">New-AzureStorageAccount -StorageAccountName 'mikefrobbins-storage' -Location 'South Central US'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage2a.jpg"><img class="alignnone size-full wp-image-11226" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage2a.jpg" alt="azure-storage2a" width="877" height="169" /></a>
<h5><em><span style="color: #ff0000;">New-AzureStorageAccount : Specified argument was out of the range of valid values.</span></em>
<em><span style="color: #ff0000;">Parameter name: parameters.Name</span></em>
<em><span style="color: #ff0000;">At line:1 char:1</span></em>
<em><span style="color: #ff0000;">+ New-AzureStorageAccount -StorageAccountName 'mikefrobbins-storage' -L ...</span></em>
<em><span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em><span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (:) [New-AzureStorageAccount], ArgumentOutOfRangeException</span></em>
<em><span style="color: #ff0000;"> + FullyQualifiedErrorId : System.ArgumentOutOfRangeException,Microsoft.WindowsAzure.Commands.ServiceManagement.Sto</span></em>
<em><span style="color: #ff0000;"> rageServices.NewAzureStorageAccountCommand</span></em></h5>
Removing the dash from the name allows the storage account to be created successfully:
<pre class="lang:ps decode:true ">New-AzureStorageAccount -StorageAccountName 'mikefrobbinsstorage' -Location 'South Central US'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage3a.jpg"><img class="alignnone size-full wp-image-11227" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage3a.jpg" alt="azure-storage3a" width="877" height="134" /></a>

Now to view the details of this new storage account that's been created:
<pre class="lang:ps decode:true ">Get-AzureStorageAccount -StorageAccountName mikefrobbinsstorage</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage4a.jpg"><img class="alignnone size-full wp-image-11228" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage4a.jpg" alt="azure-storage4a" width="877" height="360" /></a>

The description is where you can set a name that is more specific:
<pre class="lang:ps decode:true ">Set-AzureStorageAccount -StorageAccountName mikefrobbinsstorage -Description 'Mike F Robbins Storage Account for Azure South Central US Datacenter'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage5a.jpg"><img class="alignnone size-full wp-image-11229" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage5a.jpg" alt="azure-storage5a" width="877" height="169" /></a>

Now to double check to make sure the new description has been applied:
<pre class="lang:ps decode:true ">Get-AzureStorageAccount -StorageAccountName mikefrobbinsstorage</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage6a.jpg"><img class="alignnone size-full wp-image-11230" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-storage6a.jpg" alt="azure-storage6a" width="877" height="361" /></a>

That's all the time I have for this blog article. Next week, I'll be building upon this blog article for the next step in this series on managing Azure with PowerShell.

µ