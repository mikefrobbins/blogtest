---
ID: 467
post_title: >
  How to create an Administrative
  shortcut.
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/02/01/how-to-create-an-administrative-shortcut/
published: true
post_date: 2010-02-01 12:02:28
---
As most systems administrators know, you should log into your computer as a normal domain user who does not have elevated privileges in your Active Directory domain and only run administrative programs with elevated privileges when necessary. You could hold down shift, right click the shortcut, and select “Run as different user” to run a program as a user who has elevated privileges in your Active Directory domain, but there’s an easier, more efficient way to run programs that always require elevated privileges.

Either create a new shortcut or modify an existing shortcut to a program that you need to run with elevated privileges. In this example, I will be using a shortcut to an MMC console so that any snap-in I add to the MMC console will be run with elevated privileges.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_default.jpg"><img class="alignnone size-full wp-image-468" title="mmc_default" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_default.jpg" width="381" height="537" /></a>

Change the target to the following, modifying the username to a user in your domain with elevated privileges and the domain to match your domain name:
<pre class="lang:batch decode:true">C:\Windows\System32\runas.exe /user:administrator@mikefrobbins.com /env "C:\Windows\System32\mmc.exe"</pre>
<span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif; line-height: 19px; white-space: normal; font-size: 13px;"><a href="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_admin.jpg"><img class="alignnone size-full wp-image-469" title="mmc_admin" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_admin.jpg" width="390" height="537" /></a></span>

In this example, the MMC console will run as a user named administrator in the mikefrobbins.com domain. When launched, you will be prompted to enter the password for the administrator user:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_admin_password_prompt.jpg"><img class="alignnone size-full wp-image-470" title="mmc_admin_password_prompt" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_admin_password_prompt.jpg" width="600" height="304" /></a>

The same example can be applied to any shortcut. Here is an example of a shortcut to the "SQL Server Management Studio" console:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_ssms.jpg"><img class="alignnone size-full wp-image-471" title="mmc_ssms" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/02/mmc_ssms.jpg" width="381" height="537" /></a>

Change the target on the "SQL Server Management Studio" console to:
<pre class="lang:batch decode:true">C:\Windows\System32\runas.exe /user:administrator@mikefrobbins.com /env "C:\Program Files (x86)\Microsoft SQL Server\100\Tools\Binn\VSShell\Common7\IDE\Ssms.exe"</pre>
Creating administrative shortcuts to programs you frequently use that require elevated privileges is easier and more efficient than always having to do a "Run as different user" and it also helps to keep your network more secure by not being tempted to log into your computer as a user with elevated privileges in your Active Directory domain.

µ