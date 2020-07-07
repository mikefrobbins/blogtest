---
ID: 11701
post_title: >
  Deploy Desired State Configuration
  Resources with an SMB based Pull Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/05/14/deploy-desired-state-configuration-resources-with-an-smb-based-pull-server/
published: true
post_date: 2015-05-14 07:30:46
---
So you've either <a href="https://github.com/PowerShell/DscResources" target="_blank">downloaded DSC resources from GitHub</a> or you've created some DSC resources of your own and the LCM (Local Configuration Manager) on the servers in your environment is set to use an SMB based DSC pull server.

To automatically deploy those custom resources with an SMB pull server, they need to be zipped up including their base directory and named "ResourceName_Version.zip". For example, the xSmbShare DSC resource that can be download from GitHub would be named "xSmbShare_1.0.zip". In addition to this, a checksum for that zip file needs to be created. The one for xSmbShare would be named "xSmbShare_1.0.zip.checksum".

I've created a module that contains several functions that is currently a work in progress that can be used to accomplish this task:
<pre class="lang:ps decode:true ">Publish-MrDSCResourceToSMB -Name cMrSQLRecoveryModel, xSmbShare -SMBPath '\\DC01\DSCSMB'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/resourcetosmb1a.jpg"><img class="alignnone size-full wp-image-11703" src="http://mikefrobbins.com/wp-content/uploads/2015/05/resourcetosmb1a.jpg" alt="resourcetosmb1a" width="877" height="61" /></a>

You can see that both of the DSC resources that were specified in the previous command have been zipped up, the checksums created, the files have been named appropriately, and they have been uploaded to the SMB share that's being used as a DSC pull server:
<pre class="lang:ps decode:true ">Get-ChildItem -Path \\DC01\DSCSMB</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/resourcetosmb2a.jpg"><img class="alignnone size-full wp-image-11704" src="http://mikefrobbins.com/wp-content/uploads/2015/05/resourcetosmb2a.jpg" alt="resourcetosmb2a" width="877" height="216" /></a>

Based on the event logs of the server named SQL04, the cMrSQLRecoveryModel custom DSC resource  was automatically downloaded from the DSC pull server and installed:
<pre class="lang:ps decode:true">Get-MrDscLogs -ComputerName SQL04 -MaxEvents 11 | Select-Object -ExpandProperty Message</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/05/resourcetosmb3a.jpg"><img class="alignnone size-full wp-image-11707" src="http://mikefrobbins.com/wp-content/uploads/2015/05/resourcetosmb3a.jpg" alt="resourcetosmb3a" width="877" height="420" /></a>

The module that contains my DSC toolkit which I previously referenced as being a work in progress can be <a href="https://github.com/mikefrobbins/DSC" target="_blank">downloaded from GitHub</a>.

µ