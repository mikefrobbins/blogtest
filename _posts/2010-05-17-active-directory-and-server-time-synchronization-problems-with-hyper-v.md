---
ID: 810
post_title: >
  Active Directory Time Synchronization
  Problems with Hyper-V
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/05/17/active-directory-and-server-time-synchronization-problems-with-hyper-v/
published: true
post_date: 2010-05-17 17:28:08
---
One of my customers contacted me today with an issue where the time on all of their servers was off by about 8 minutes or so. My first thought was “which Active Directory domain controller is their authoritative time server?” and “I’ll update the time on it manually and then set it up to synchronize from an Internet time server”.

By default, the authoritative time server for your organization is the server that holds the PDC Emulator FSMO role in the forest root domain. You can run “netdom /query fsmo” from a machine in the forest root domain to determine which DC this role is running on. Each domain has a PDC Emulator, but you want the one in the forest root domain.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/netdom_fsmo.png"><img class="alignnone size-full wp-image-811" title="netdom_fsmo" src="http://mikefrobbins.com/wp-content/uploads/2010/05/netdom_fsmo.png" alt="" width="402" height="125" /></a>

Once I had this information, I manually adjusted the time on that domain controller, but within 5 seconds the time changed back to being about 8 minutes or so off. I stopped the windows time service (w32time) thinking that would keep the time from updating automatically, but I experienced the same problem again.

The server was synchronizing its time from the “VM IC Time Synchronization Provider” based on the results of running “w32tm /query /status” from the command line of the DC:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status.png"><img class="alignnone size-full wp-image-812" title="w32tm_querry_status" src="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status.png" alt="" width="408" height="159" /></a>

This meant the domain controller that was hosting the PDC Emulator FSMO role for the forest root domain was running as a guest operating system on top of a Hyper-V virtualization server, and it was synchronizing its time from the Hyper-V host OS which was indeed about 8 minutes off. A quick time adjustment to the Hyper-V host OS and the time was correct, at least for now. To keep the guest operating system from synchronizing its time from the host operating system, I went into Hyper-V Manager and disabled the “Time synchronization” Service under “Management&gt;Integration Services”:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/hv_man_time.png"><img class="alignnone size-full wp-image-813" title="hv_man_time" src="http://mikefrobbins.com/wp-content/uploads/2010/05/hv_man_time.png" alt="" width="431" height="347" /></a>

Once complete, I ran “w32tm /resync /rediscover” on the domain controller:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm__resync_rediscover.png"><img class="alignnone size-full wp-image-814" title="w32tm__resync_rediscover" src="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm__resync_rediscover.png" alt="" width="327" height="64" /></a>

If this command fails, check to make sure you have access to the Internet:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm__resync_rediscover_failure.png"><img class="alignnone size-full wp-image-815" title="w32tm__resync_rediscover_failure" src="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm__resync_rediscover_failure.png" alt="" width="508" height="67" /></a>

I then ran “w32tm /query /status” on the domain controller:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status_cmos.png"><img class="alignnone size-full wp-image-816" title="w32tm_querry_status_cmos" src="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status_cmos.png" alt="" width="428" height="159" /></a>

Now the source to synchronize its time from was “Local CMOS Clock” which is the default.

I have read the best practices for configuring an authoritative time server which states that it is recommended to use a hardware source for the time instead of a time server on the Internet, but unless you have some type of Atomic Clock hardware to synchronize from, or you just enjoy manually adjusting the time on your server, I recommend synchronizing the time from an Internet time server.

I followed the “Configuring the Windows Time service to use an external time source” procedure found in <a href="http://support.microsoft.com/kb/816042" target="_blank">Microsoft Knowledge Base Article 816042</a>.

Once everything was configured based on this article, I restarted the Windows Time Service (w32Time), ran “w32tm /resync /rediscover”, and then “w32tm /query /status” which showed the server was now synchronizing it’s time from an Internet time server:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status_inet.png"><img class="alignnone size-full wp-image-817" title="w32tm_querry_status_inet" src="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status_inet.png" alt="" width="423" height="159" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/w32tm_querry_status_inet.png"></a> µ