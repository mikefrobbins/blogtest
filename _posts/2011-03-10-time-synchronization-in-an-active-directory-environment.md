---
ID: 1959
post_title: >
  Time Synchronization in an Active
  Directory Environment
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/03/10/time-synchronization-in-an-active-directory-environment/
published: true
post_date: 2011-03-10 07:30:33
---
In an Active Directory environment the default time source is the domain controller in your forest root domain that is running the PDC emulator FSMO role. Keep in mind that the PDC emulator FSMO role is a domain level FSMO role so each domain will have one, but each domain’s PDC emulator will receive its time from the forest root’s PDC emulator. The following procedure will walk you through the steps of configuring the forest root’s PDC emulator to receive its time updates from an Internet time server.

If you don’t know which one of your Active Directory domains is the forest root domain, there are several articles on the Internet to easily determine which one is the forest root. Here’s one of those articles at <a href="http://www.windowsitpro.com/article/domains2/how-can-i-determine-which-domain-is-the-forest-root-domain-.aspx" target="_blank">Windows IT Pro</a>.

From a domain controller in the forest root domain, determine the PDC emulator by running netdom /query fsmo
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync1.png"><img class="alignnone size-full wp-image-1961" title="time-sync1" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync1.png" alt="" width="414" height="127" /></a>

On the PDC emulator, configure the following registry value to “5”:
HKLM&gt;System&gt;CurrentControlSet&gt;Services&gt;W32Time&gt;Config&gt;AnnounceFlags
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync2.png"><img class="alignnone size-full wp-image-1962" title="time-sync2" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync2.png" alt="" width="640" height="173" /></a>

Configure the following registry value to “1800” decimal (30 minutes):
HKLM&gt;System&gt;CurrentControlSet&gt;Services&gt;W32Time&gt;Config&gt;MaxNegPhaseCorrection
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync3.png"><img class="alignnone size-full wp-image-1963" title="time-sync3" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync3.png" alt="" width="640" height="173" /></a>

Configure the following registry value to “1800” decimal (30 minutes):
HKLM&gt;System&gt;CurrentControlSet&gt;Services&gt;W32Time&gt;Config&gt;MaxPosPhaseCorrection
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync4.png"><img class="alignnone size-full wp-image-1964" title="time-sync4" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync4.png" alt="" width="640" height="173" /></a>

Configure the following registry value to “tock.usno.navy.mil,0x1”:
HKLM&gt;System&gt;CurrentControlSet&gt;Services&gt;W32Time&gt;Parameters&gt;NtpServer
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync5.png"><img class="alignnone size-full wp-image-1965" title="time-sync5" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync5.png" alt="" width="640" height="173" /></a>

Configure the following registry value to “NTP”:
HKLM&gt;System&gt;CurrentControlSet&gt;Services&gt;W32Time&gt;Parameters&gt;Type
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync6.png"><img class="alignnone size-full wp-image-1966" title="time-sync6" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync6.png" alt="" width="640" height="173" /></a>

Configure the following registry value to “900” decimal (15 minutes): HKLM&gt;System&gt;
CurrentControlSet&gt;Services&gt;W32Time&gt;TimeProviders&gt;NtpClient&gt;SpecialPollInterval
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync7.png"><img class="alignnone size-full wp-image-1967" title="time-sync7" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync7.png" alt="" width="640" height="173" /></a>

Verify the following registry value is set to “1”: HKLM&gt;System&gt;CurrentControlSet&gt;
Services&gt;W32Time&gt;TimeProviders&gt;NtpServer&gt;Enabled
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync8.png"><img class="alignnone size-full wp-image-1968" title="time-sync8" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync8.png" alt="" width="640" height="173" /></a>

Restart the Windows Time (w32time) service.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync9.png"><img class="alignnone size-full wp-image-1969" title="time-sync9" src="http://mikefrobbins.com/wp-content/uploads/2011/02/time-sync9.png" alt="" width="403" height="112" /></a>

If any of the PDC emulators are running as a virtualization guest on a Hyper-V server, see my “<a href="http://mikefrobbins.com/2010/05/17/active-directory-and-server-time-synchronization-problems-with-hyper-v/" target="_blank">Active Directory Time Synchronization Problems with Hyper-V</a>” blog.

µ