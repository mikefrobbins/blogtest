---
ID: 6781
post_title: >
  Unable to View a Shared Desktop or
  Application in a Microsoft Lync 2013
  Meeting
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/02/21/unable-to-view-a-shared-desktop-or-application-in-a-microsoft-lync-2013-meeting/
published: true
post_date: 2013-02-21 07:30:44
---
Recently while using Microsoft Lync 2013 to view a shared desktop or application in a meeting, I experienced an issue where I could only see a blank white screen as shown in the image below:

<img class="alignnone size-full wp-image-6782" alt="lync1" src="http://mikefrobbins.com/wp-content/uploads/2013/02/lync1.png" width="640" height="454" />

Event ID 301, Microsoft Office 15 Alerts was logged in the event log with the following details:

<em>Microsoft Lync</em>
<em>CLSID: {00000000-0000-0000-0000-000000000000}</em>
<em>CATID: {9B179D6E-9BDB-454b-BE3D-89F9A792BD39}</em>
<em>The ActiveX Compatibility setting disabled loading this object to help protect your security.</em>
<em>P1: 1073695582</em>
<em>P2: 15.0.4420.1017</em>
<em id="__mceDel">P3:
P4: 0x80004005</em>

<img class="alignnone size-full wp-image-6783" alt="lync2" src="http://mikefrobbins.com/wp-content/uploads/2013/02/lync2.png" width="640" height="445" />

I tracked this down to the following registry key: "HKEY_LOCAL_MACHINESOFTWAREMicrosoftInternet ExplorerActiveX Compatibility{00000000-0000-0000-0000-000000000000}"

<img class="alignnone size-full wp-image-6784" alt="lync3" src="http://mikefrobbins.com/wp-content/uploads/2013/02/lync3.png" width="640" height="152" />

I backed up this registry key and then removed it using PowerShell:
<pre class="lang:ps decode:true">Remove-Item -Path 'HKLM:\SOFTWARE\Microsoft\Internet Explorer\ActiveX Compatibility\{00000000-0000-0000-0000-000000000000}'</pre>
<img class="alignnone size-full wp-image-6785" alt="lync4" src="http://mikefrobbins.com/wp-content/uploads/2013/02/lync4.png" width="640" height="50" />

Now the registry key is gone:

<img class="alignnone size-full wp-image-6786" alt="lync5" src="http://mikefrobbins.com/wp-content/uploads/2013/02/lync5.png" width="640" height="152" />

Then the issue with viewing a shared desktop or applicaiton in Lync 2013 was resolved:

<img class="alignnone size-full wp-image-6787" alt="lync6" src="http://mikefrobbins.com/wp-content/uploads/2013/02/lync6.png" width="640" height="454" />

µ