---
ID: 5976
post_title: 'PowerShell Studio 2012: PowerShell Cache Builder has Stopped Working'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/27/powershell-studio-2012-powershell-cache-builder-has-stopped-working/
published: true
post_date: 2012-11-27 07:30:01
---
I won a copy of <a href="http://www.sapien.com/software/powershell_studio" target="_blank">PowerShell Studio 2012</a> at <a href="http://powershellsaturday.com/003/" target="_blank">PowerShell Saturday 003</a> in Atlanta last month. It's an awesome product to win and the best giveaway of the entire event (in my opinion).

I ran into an issue the first time I ran the product after the installation and thought I would share the problem, cause, and solution so anyone who experiences the same issue can easily resolve it. The installation itself completed without issue. The first time I opened the program, I received the error in the following image:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error1.png"><img class="alignnone size-full wp-image-5977" title="cachebuilder-error1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error1.png" height="452" width="526" /></a>

Based on the information in the previous image, I can see it seems to be having an issue with the Microsoft Exchange 2010 Management Tools that I have installed which includes the Exchange 2010 PowerShell snapin.

The problem this created in PowerShell Studio, is it only allowed 32 bit mode:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error2.png"><img class="alignnone size-full wp-image-5978" title="cachebuilder-error2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error2.png" height="189" width="576" /></a>

Maybe hacking my Windows 8 installation to force the installation of the Exchange 2010 Management Tools wasn't such a good idea after all. Reference: "<a href="http://mikefrobbins.com/2012/08/16/how-to-install-the-exchange-2010-management-tools-on-windows-8-rtm/" target="_blank">How to Install the Exchange 2010 Management Tools on Windows 8 RTM</a>".

I uninstalled the Exchange 2010 Management Tools:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error3.png"><img class="alignnone size-full wp-image-5979" title="cachebuilder-error3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error3.png" height="68" width="640" /></a>

I then re-ran the installation of PowerShell Studio 2012 and performed a repair installation. Once the repair was complete, the Cache Builder completed successfully and both 32 and 64 bit were available from the drop-down list:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error4.png"><img class="alignnone size-full wp-image-5980" title="cachebuilder-error4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/cachebuilder-error4.png" height="406" width="640" /></a>

µ