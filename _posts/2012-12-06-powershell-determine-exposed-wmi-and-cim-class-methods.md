---
ID: 6196
post_title: 'PowerShell: Determine Exposed WMI and CIM Class Methods'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/12/06/powershell-determine-exposed-wmi-and-cim-class-methods/
published: true
post_date: 2012-12-06 07:30:20
---
I sat through the <a href="http://www.azposh.com/" target="_blank">Arizona PowerShell User Group</a> meeting yesterday evening via a Lync online meeting. Don Jones was the guest speaker this month. His session was on generating reports from PowerShell. It was a super cool session and if you're not taking advantage of user group meetings, you're really missing out. <a href="http://www.interfacett.com/" target="_blank">Interface Technical Training</a> was the sponsor for the user group meeting this month. I'm currently working my way through their PowerShell video training series which I've been very impressed with so far. A subscription is $25 a month for full access to all of their <a href="http://videotraining.interfacett.com/" target="_blank">training videos</a> which includes more than just PowerShell videos.

During last months Arizona PowerShell User Group meeting, Aleksandar Nikolić, a PowerShell MVP, posted a number of great tips in the chat during The Scripting Guy (Ed Wilson)'s presentation which was on managing Windows 8 remotely with PowerShell version 3 (An awesome session as well).

One of the code snippets that Aleksandar posted was how to determine what the exposed WMI and CIM class methods are. Here's the code snippet which returns a list of the exposed methods for the Win32_Process class:
<pre class="lang:ps decode:true">(get-cimclass win32_process).CimClassMethods</pre>
<a href="http://mikefrobbins.com/2012/12/06/powershell-determine-exposed-wmi-and-cim-class-methods/cim-methods1/" rel="attachment wp-att-6197"><img class="alignnone size-full wp-image-6197" alt="cim-methods1" src="http://mikefrobbins.com/wp-content/uploads/2012/12/cim-methods1.png" width="640" height="130" /></a>

Aleksandar also posted these other code snippets which are all very interesting:
<pre class="lang:ps decode:true">dir wsman:\localhost\service\defaultports</pre>
<a href="http://mikefrobbins.com/2012/12/06/powershell-determine-exposed-wmi-and-cim-class-methods/wsman-ports1/" rel="attachment wp-att-6201"><img class="alignnone size-full wp-image-6201" alt="wsman-ports1" src="http://mikefrobbins.com/wp-content/uploads/2012/12/wsman-ports1.png" width="585" height="123" /></a>
<pre class="lang:ps decode:true">dir wsman:\localhost\service</pre>
<a href="http://mikefrobbins.com/2012/12/06/powershell-determine-exposed-wmi-and-cim-class-methods/wsman-service1/" rel="attachment wp-att-6202"><img class="alignnone size-full wp-image-6202" alt="wsman-service1" src="http://mikefrobbins.com/wp-content/uploads/2012/12/wsman-service1.png" width="640" height="277" /></a>
<pre class="lang:ps decode:true">get-pssessionconfiguration microsoft.powershell | fl *</pre>
<a href="http://mikefrobbins.com/2012/12/06/powershell-determine-exposed-wmi-and-cim-class-methods/pssession-config1/" rel="attachment wp-att-6203"><img class="alignnone size-full wp-image-6203" alt="pssession-config1" src="http://mikefrobbins.com/wp-content/uploads/2012/12/pssession-config1.png" width="640" height="460" /></a>

µ