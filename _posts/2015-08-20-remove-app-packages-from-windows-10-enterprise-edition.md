---
ID: 12239
post_title: >
  Remove App Packages from Windows 10
  Enterprise Edition
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/08/20/remove-app-packages-from-windows-10-enterprise-edition/
published: true
post_date: 2015-08-20 07:30:03
---
So you've installed Windows 10 enterprise edition only to find applications that you would consider to be consumer type apps such as Bing Finance, News, and Sports which is not what you would normally expect to find in an enterprise edition operating system version:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware1b.jpg"><img class="alignnone size-full wp-image-12273" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware1b.jpg" alt="win10-bloatware1b" width="888" height="622" /></a>

You can obtain a list of these app packages for the current user with the <em><a href="https://technet.microsoft.com/en-us/library/Dn448380.aspx" target="_blank">Get-AppxPackage</a></em> PowerShell cmdlet. I've sorted the list of app packages by name in the following results:
<pre class="lang:ps decode:true">Get-AppxPackage | Sort-Object -Property Name | Select-Object -Property Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware2c.jpg"><img class="alignnone size-full wp-image-12243" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware2c.jpg" alt="win10-bloatware2c" width="859" height="741" /></a>

I've determined which app packages that I want to remove and I'll remove them for the current user with the following PowerShell script:
<pre class="lang:ps decode:true ">'Microsoft.3DBuilder', 'Microsoft.BingFinance', 'Microsoft.BingNews',
'Microsoft.BingSports', 'Microsoft.MicrosoftSolitaireCollection',
'Microsoft.People', 'Microsoft.Windows.Photos', 'Microsoft.WindowsCamera',
'microsoft.windowscommunicationsapps', 'Microsoft.WindowsPhone',
'Microsoft.WindowsSoundRecorder', 'Microsoft.XboxApp', 'Microsoft.ZuneMusic',
'Microsoft.ZuneVideo' |
ForEach-Object {
    Get-AppxPackage -Name $_ |
    Remove-AppxPackage
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware3a.jpg"><img class="alignnone size-full wp-image-12244" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware3a.jpg" alt="win10-bloatware3a" width="859" height="275" /></a>

The app packages that were previously removed no longer exist for the current user:
<pre class="lang:ps decode:true ">Get-AppxPackage | Sort-Object -Property Name | Select-Object -Property Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware13a.jpg"><img class="alignnone size-full wp-image-12275" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware13a.jpg" alt="win10-bloatware13a" width="859" height="552" /></a>

One thing to keep in mind is this occurs for <span style="text-decoration: underline;">the current user</span> so if you're logging into your machine as a non-admin (which I highly recommend) and you run PowerShell elevated, whatever user you specify when you're prompted for elevation is the user account the applications will be removed for. This will make it appear as if the command didn't work.

<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware4a.jpg"><img class="alignnone size-full wp-image-12245" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware4a.jpg" alt="win10-bloatware4a" width="452" height="460" /></a>

The secret to removing these applications for the current user is that PowerShell doesn't need to run elevated (run an admin) as shown in the previous examples where the PowerShell console doesn't say "Administrator" in the title bar of the console window.

When complete, you should notice that those applications have been removed from your start menu, screen, or whatever they're calling it now days:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware5b.jpg"><img class="alignnone size-full wp-image-12274" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware5b.jpg" alt="win10-bloatware5b" width="888" height="620" /></a>

<em>Get-AppxPackage</em> does have an <em>AllUsers</em> parameter but that only retrieves applications for all users and doesn't remove them for all users when piping to <em><a href="https://technet.microsoft.com/en-us/library/Dn448374.aspx" target="_blank">Remove-AppxPackage</a>.</em>

You could also walk through the applications one by one and remove the desired ones by simply piping <em>Get-AppxPackage</em> to <em>Remove-AppxPackage</em> using the <em>Confirm</em> parameter:
<pre class="lang:ps decode:true  ">Get-AppxPackage | Remove-AppxPackage -Confirm</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware6a.jpg"><img class="alignnone size-full wp-image-12252" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware6a.jpg" alt="win10-bloatware6a" width="859" height="132" /></a>

What if you want to prevent these applications from being installed for any new user that's created on your machine? Well, you're in luck because these's a cmdlet for that.

The <em><a href="https://technet.microsoft.com/en-us/library/Hh852131.aspx" target="_blank">Get-AppxProvisionedPackage</a></em> cmdlet retrieves a list of app packages that are set to be installed for each new user that's created. Note that this command does require elevation.
<pre class="lang:ps decode:true">Get-AppxProvisionedPackage -Online | Sort-Object -Property DisplayName | Select-Object -Property DisplayName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware7a.jpg"><img class="alignnone size-full wp-image-12259" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware7a.jpg" alt="win10-bloatware7a" width="859" height="409" /></a>

When combined with the <em><a href="https://technet.microsoft.com/en-us/library/Hh852131.aspx" target="_blank">Remove-ProvisionedAppxPackage</a></em> cmdlet, specific app packages can be removed from the current operating system image:
<pre class="lang:ps decode:true ">$Packages = 'Microsoft.3DBuilder', 'Microsoft.BingFinance', 'Microsoft.BingNews',
'Microsoft.BingSports', 'Microsoft.MicrosoftSolitaireCollection',
'Microsoft.People', 'Microsoft.Windows.Photos', 'Microsoft.WindowsCamera',
'microsoft.windowscommunicationsapps', 'Microsoft.WindowsPhone',
'Microsoft.WindowsSoundRecorder', 'Microsoft.XboxApp', 'Microsoft.ZuneMusic',
'Microsoft.ZuneVideo'

Get-AppxProvisionedPackage -Online |
Where-Object DisplayName -In $Packages |
Remove-ProvisionedAppxPackage -Online |
Out-Null</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware8a.jpg"><img class="alignnone size-full wp-image-12260" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware8a.jpg" alt="win10-bloatware8a" width="859" height="167" /></a>

As you can see, the specified apps have been removed from the operating system image:
<pre class="lang:ps decode:true ">Get-AppxProvisionedPackage -Online | Sort-Object -Property DisplayName | Select-Object -Property DisplayName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware9a.jpg"><img class="alignnone size-full wp-image-12261" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-bloatware9a.jpg" alt="win10-bloatware9a" width="859" height="239" /></a>

The <em><a href="https://technet.microsoft.com/en-us/library/Hh852174.aspx" target="_blank">Remove-AppxProvisionedPackage</a></em> cmdlet doesn't remove the app packages that are already installed for any preexisting users, but it does prevent any new users from receiving the app packages that are removed from the operating system image.

<span style="text-decoration: underline;">Update</span>
I received notification from fellow PowerShell MVP <a href="https://p0w3rsh3ll.wordpress.com/" target="_blank">Emin Atac</a> that there's a known problem where sysprep fails after removing or updating Windows built-in Windows Store apps as referenced in <a href="http://support.microsoft.com/en-us/kb/2769827" target="_blank">KB2769827</a> so take that into consideration when removing apps.

µ