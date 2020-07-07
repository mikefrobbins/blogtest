---
ID: 1239
post_title: >
  Get-OWAVirtualDirectory Access is Denied
  on Exchange 2010
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/09/02/get-owavirtualdirectory-access-is-denied-exchange-2010/
published: true
post_date: 2010-09-02 05:30:25
---
When attempting to open "Server Configuration&gt;Client Access" from the Exchange Management Console, you receive the following error:
<em>An IIS directory entry couldn’t be created. The error message is Access is denied. HResult = -2147024891 It was running the command ‘Get-OwaVirtualDirectory’.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owadir1.jpg"><img class="alignnone size-full wp-image-1241" title="owadir1" src="http://mikefrobbins.com/wp-content/uploads/2010/09/owadir1.jpg" alt="" width="500" height="128" /></a></em>

Running the command listed in the error in the Exchange Management Shell gives you some additional information:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/owadir21.jpg"><img class="alignnone size-full wp-image-1246" title="owadir2" src="http://mikefrobbins.com/wp-content/uploads/2010/09/owadir21.jpg" alt="" width="640" height="67" /></a>

To resolve this issue, add the "domainExchange Trusted Subsystem" group to the local administrators group of all of your exchange servers<em>.</em>

µ