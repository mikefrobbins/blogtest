---
ID: 10415
post_title: >
  Unlock a Volume Protected with BitLocker
  Drive Encryption
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/09/15/unlock-a-volume-protected-with-bitlocker-drive-encryption/
published: true
post_date: 2014-09-15 07:30:11
---
Have a Windows Server 2012 R2 machine that runs the Server Core (no-GUI) installation of the operating system? Maybe that server has a volume that is protected with <a href="http://en.wikipedia.org/wiki/BitLocker" target="_blank">BitLocker Drive Encryption</a>? If so, how would you unlock the encryption so you can access the data on that volume without using a graphical user interface?

With PowerShell of course, specifically the <a href="http://technet.microsoft.com/en-us/library/jj649833.aspx" target="_blank"><em>Unlock-BitLocker</em></a> PowerShell cmdlet:
<pre class="lang:ps decode:true">Unlock-BitLocker -MountPoint 'D:' -Password (Read-Host 'Enter Password' -AsSecureString)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/unlock-bitlocker1.png"><img class="alignnone size-full wp-image-10416" src="http://mikefrobbins.com/wp-content/uploads/2014/09/unlock-bitlocker1.png" alt="unlock-bitlocker1" width="875" height="191" /></a>

µ