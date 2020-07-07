---
ID: 2356
post_title: >
  Enabling Dynamic Memory for a Guest VM
  on Hyper-V
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/06/09/enabling-dynamic-memory-for-a-guest-vm-on-hyper-v/
published: true
post_date: 2011-06-09 07:30:17
---
You've loaded SP1 on your Windows Server 2008 R2 Hyper-V virtualization host server and you're ready to begin using Dynamic Memory for your virtualization guest machines (VM's). Listed below is the minimum amount of changes required for your virtualization guest machines (VM’s) to be able to use dynamic memory:

Set the VM to use Dynamic Memory by specifying a minimum and maximum amount of memory. The VM will need to be shutdown in order to change this setting and the settings will only show up if the computer running Hyper-V manager has SP1 installed.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/dm-setting.png"><img class="alignnone size-full wp-image-2362" title="dm-setting" src="http://mikefrobbins.com/wp-content/uploads/2011/06/dm-setting.png" alt="" width="493" height="316" /></a>

<strong>Windows Server 2008 with SP2:</strong>  Web and Standard edition, update the integration services components and load a specific <a href="http://support.microsoft.com/kb/2230887" target="_blank">HotFix</a>. You'll have to request the hotfix and the email you'll receive from Microsoft support has Vista in the URL link, but it does indeed fix Dynamic Memory for these two versions of 2008. For Enterprise edition or higher, update the integration services in the guest VM.

<strong>Windows Server 2008 R2:</strong> For Web and Standard edition, SP1 must be installed on the guest VM. For Enterprise edition or higher, update the integration services in the guest VM or load SP1 on the guest VM.

<strong>Windows 7:</strong> Ultimate and Enterprise editions, update the integration services in the guest VM.

<strong>Windows Vista with SP1:</strong> Update the integration services in the guest VM.

<strong>Windows Server 2003 with SP2 and 2003 R2 with SP2:</strong> Update the integration services in the guest VM.

Something to keep in mind is that updating the integration services components for a guest VM does require it to be restarted. There's a <a href="http://technet.microsoft.com/en-us/library/ff817651(WS.10).aspx" target="_blank">Hyper-V Dynamic Memory Configuration Guide</a> on Microsoft TechNet. The appendix section lists recommended memory startup values for your guest VM's, but based on my experiences, I would not follow those recommendations. Things like Backup Exec backup jobs will slow to a crawl when the guest VM is set to startup with too little memory. If you wouldn't order a server with less than 2GB of RAM, then that is a good minimum value and set the maximum value to the amount of RAM you were giving the VM prior to using Dynamic Memory. Here are some of the values I use:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/dm-example.png"><img class="alignnone size-full wp-image-2357" title="dm-example" src="http://mikefrobbins.com/wp-content/uploads/2011/06/dm-example.png" alt="" width="328" height="214" /></a>

µ