---
ID: 11251
post_title: >
  Use PowerShell to Create a Linux VM in
  Azure
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/03/12/use-powershell-to-create-a-linux-vm-in-azure/
published: true
post_date: 2015-03-12 07:30:01
---
In a couple of my previous blog articles, I've demonstrated <a href="http://mikefrobbins.com/2015/02/19/use-powershell-to-create-a-storage-account-in-azure/" target="_blank">how to create a storage account in Azure</a> and <a href="http://mikefrobbins.com/2015/02/26/use-powershell-to-create-a-reserved-virtual-ip-address-vip-in-azure/?preview=true" target="_blank">how to create a reserved virtual IP address in Azure</a>. Both of those items will be used in today's blog article so I recommend reading through those previous blog articles if you haven't already done so.

The goal in this blog article is to build a CentOS based OpenLogic 7.0 VM in Azure except using PowerShell instead using the Azure portal website (GUI):

<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos8a.jpg"><img class="alignnone size-full wp-image-11282" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos8a.jpg" alt="azure-centos8a" width="919" height="495" /></a>

First, the name of the image that Azure uses to build those VM's will need to be determined. This can be accomplished using the <em>Get-AzureVMImage</em> cmdlet:
<pre class="lang:ps decode:true ">Get-AzureVMImage |
Where-Object ImageName -like *centos* |
Select-Object -Property ImageName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos1a.jpg"><img class="alignnone size-full wp-image-11252" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos1a.jpg" alt="azure-centos1a" width="877" height="420" /></a>

The last one in the previous set of results looks like the image that's used to create the CentOS based OpenLogic 7.0 VM's on Azure.

Just to be sure, let's take a look at the details of that particular image:
<pre class="lang:ps decode:true">Get-AzureVMImage -ImageName 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150128</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos2a.jpg"><img class="alignnone size-full wp-image-11253" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos2a.jpg" alt="azure-centos2a" width="877" height="418" /></a>

Yep. That's the image. You can also see what locations that particular image is available in as shown in the previous results.

You could also return a list of the locations where that image is available in a more readable format by selecting only the location property, splitting on the semicolon, and sorting by name:
<pre class="lang:ps decode:true ">(Get-AzureVMImage -ImageName 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150128).Location -split ';' |
Sort-Object</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos9a.jpg"><img class="alignnone size-full wp-image-11284" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos9a.jpg" alt="azure-centos9a" width="877" height="226" /></a>

Now to create a cloud service in Azure for our new VM:
<pre class="lang:ps decode:true ">New-AzureService -ServiceName 'mikefrobbins-test' -Location 'South Central US'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos3a.jpg"><img class="alignnone size-full wp-image-11255" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos3a.jpg" alt="azure-centos3a" width="877" height="130" /></a>

I've used the process shown in <a href="http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-use-ssh-key/#create-a-private-key-on-windows" target="_blank">this article on Azure</a> to create SSH keys for my Linux VM. I didn't install CYGWIN as that article suggested. I followed the process <a href="http://www.eclectica.ca/howto/ssl-cert-howto.php#setup" target="_blank">here</a> to install and configure OpenSSL and I used Win32 OpenSSL which can be downloaded from <a href="http://slproweb.com/products/Win32OpenSSL.html" target="_blank">here</a>. I also installed <a href="http://www.putty.org/" target="_blank">Putty</a> which the article on Azure that I previously referenced suggested.

The public key certificate that was created as shown in that previously referenced Azure article needs to be uploaded to the cloud service that was previously created. It also needs to be added to the user's account on the Linux VM that we'll be creating:
<pre class="lang:ps decode:true">$certificate = 'C:\tmp\mikefrobbinsCert.cer'
Add-AzureCertificate -CertToDeploy $certificate -ServiceName 'mikefrobbins-test'
$sshkey = New-AzureSSHKey -PublicKey -Fingerprint (Get-PfxCertificate -FilePath $certificate).Thumbprint -Path '/home/mrtest/.ssh/authorized_keys'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos4b.jpg"><img class="alignnone size-full wp-image-11260" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos4b.jpg" alt="azure-centos4b" width="877" height="169" /></a>

If you haven't already associated a storage account with your Azure subscription, that will need to be accomplished prior to creating the VM, otherwise you'll receive an error message stating that a default storage account hasn't been configured or to specify a media location for the VM. This storage account must reside in the same data-center as the VM.
<pre class="lang:ps decode:true">Set-AzureSubscription -SubscriptionId 'abcdefgh-1234-1a23-a123-ab123456c78c' -CurrentStorageAccountName 'mikefrobbinsstorage'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/azure-centos5b.jpg"><img class="alignnone size-full wp-image-11292" src="http://mikefrobbins.com/wp-content/uploads/2015/03/azure-centos5b.jpg" alt="azure-centos5b" width="877" height="72" /></a>

Create an Azure VM config using the <em>New-AzureVMConfig</em> cmdlet, pipe that to <em>Add-AzureProvisioningConfig</em> to set some of the additional options, and then pipe that to <em>New-AzureVM</em> to create the actual VM:
<pre class="lang:ps decode:true">New-AzureVMConfig -ImageName '5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150128' -InstanceSize 'Basic_A1' -Name 'mrtestvm01' |
Add-AzureProvisioningConfig -Linux -LinuxUser 'mrtest' -NoSSHPassword -SSHPublicKeys $sshKey |
New-AzureVM -ServiceName 'mikefrobbins-test' -ReservedIPName 'mikefrobbinsIP' -Location 'South Central US'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos6a.jpg"><img class="alignnone size-full wp-image-11258" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos6a.jpg" alt="azure-centos6a" width="877" height="191" /></a>

Be patient, the previous command will take a couple of minutes to complete.

I've opened the Putty SSH client and logged into the new VM that was created in the prevous step. I'll verify the version of CentOS that it's running:
<pre class="lang:sh decode:true  ">cat /etc/centos-release</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos7a.jpg"><img class="alignnone size-full wp-image-11262" src="http://mikefrobbins.com/wp-content/uploads/2015/02/azure-centos7a.jpg" alt="azure-centos7a" width="675" height="90" /></a>

If you're looking for a client to transfer files to/from your new Linux VM, I'd recommend <a href="http://winscp.net/eng/index.php" target="_blank">WinSCP</a>. It's easy to setup and use and it supports using certificates.

µ