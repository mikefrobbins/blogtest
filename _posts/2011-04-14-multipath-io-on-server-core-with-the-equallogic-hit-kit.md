---
ID: 2157
post_title: >
  MultiPath I/O on Server Core with the
  EqualLogic HIT Kit
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/04/14/multipath-io-on-server-core-with-the-equallogic-hit-kit/
published: true
post_date: 2011-04-14 07:30:10
---
I’ve written a few other blogs about <a href="http://en.wikipedia.org/wiki/ISCSI" target="_blank">iSCSI</a> and <a href="http://en.wikipedia.org/wiki/Multipath_I/O" target="_blank">Multipath I/O</a> on Windows Servers, but this one focuses on installing the EqualLogic Host Integration Tool (HIT) Kit on Windows Server 2008 R2 Core (no GUI). If you are using an EqualLogic SAN, I recommend installing the HIT kit before doing any of the iSCSI or Multipath I/O configuration. It will make your life a lot easier. It’s also not a problem to install the HIT kit after you’ve done some or all of the configuration, just keep in mind there will be a few dialog boxes in this blog that you won't see such as the HIT kit wanting to install the Multipath I/O feature.

Login to your <a href="http://support.dell.com/equallogic" target="_blank">EqualLogic support account</a> or request a support account from the login page if you don’t already have one. Download the latest version of the "Host Integration Toolkit for Microsoft" from the EqualLogic support site.  Run setup64.exe:  <a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit1.png"><img class="alignnone size-full wp-image-2161" title="mpath-hit1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit1.png" width="125" height="20" /></a>

Click "Next" on the first screen. You're probably wondering if this is being installed on server core (it is). Accept the License on the next screen and then select custom on the screen after that one:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit4.png"><img class="alignnone size-full wp-image-2163" title="mpath-hit4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit4.png" width="640" height="479" /></a>

I took the defaults for the installation location. If you have not done any iSCSI configuration already, you will receive this message. Click “Yes”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit6.png"><img class="alignnone size-full wp-image-2164" title="mpath-hit6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit6.png" width="405" height="191" /></a>

Choose the appropriate option for your environment. I chose “Yes”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit7.png"><img class="alignnone size-full wp-image-2165" title="mpath-hit7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit7.png" width="407" height="217" /></a>

I took the defaults on the "Select Components" screen. Click install. You will receive the following message if Multipath I/O is not already installed. Click “Yes”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit10.png"><img class="alignnone size-full wp-image-2166" title="mpath-hit10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit10.png" width="416" height="256" /></a>

Click “Finish”. Leaving the “Launch Remote Setup Wizard” check-mark checked will launch it after you reboot and log back in. Click “OK” to restart. Once the server restarts, log back in, select “Configure MPIO settings for this computer”, and click “Next”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit14.png"><img class="alignnone size-full wp-image-2167" title="mpath-hit14" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit14.png" width="552" height="379" /></a>

Move your LAN subnet(s) to the “Subnets excluded from MPIO” side and click “Finish”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit15.png"><img class="alignnone size-full wp-image-2168" title="mpath-hit15" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit15.png" width="552" height="379" /></a>

Launch the iSCSI control panel applet by running iscsicpl.exe:  <a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit161.png"><img class="alignnone size-full wp-image-2180" title="mpath-hit16" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit161.png" width="109" height="18" /></a>

You’ll notice that you now have an additional tab named “Dell EqualLogic MPIO”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit17.png"><img class="alignnone size-full wp-image-2170" title="mpath-hit17" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit17.png" width="485" height="241" /></a>

Enter the group IP address of your SAN in the discovery portal section on the discovery tab and connect to a target on the targets tab as shown in my "<a href="http://mikefrobbins.com/2010/08/19/iscsi-initiator-configuration-on-windows-2008-r2-server-core/" target="_blank">iSCSI Initiator Configuration on Windows 2008 R2 Server Core</a>" blog. That's assuming that you've already configured a volume on your SAN for this server to access. If you have not, you can find those instructions on my "<a href="http://mikefrobbins.com/2010/08/12/dell-equallogic-ps4000-creating-a-volume/" target="_blank">Dell EqualLogic PS4000 – Creating a Volume</a>" blog and if you haven't done any configuration at all on your SAN, see my "<a href="http://mikefrobbins.com/2010/08/05/initial-configuration-of-a-dell-equallogic-ps4000xv-san/" target="_blank">Initial Configuration of a Dell EqualLogic PS4000XV SAN</a>" blog. One last thing while we're on the subject of other blog articles: Have you updated the firmware on your SAN? You can find that blog here: "<a href="http://mikefrobbins.com/2011/01/27/updating-the-firmware-on-an-equallogic-ps4000xv-san/" target="_blank">Updating the Firmware on an EqualLogic PS4000XV SAN</a>".

Select the “Enable multi-path” option when connecting to the target:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit181.png"><img class="alignnone size-full wp-image-2228" title="mpath-hit181" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit181.png" width="416" height="210" /></a>

After a few seconds, you’ll see a single connection to the target:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit19.png"><img class="alignnone size-full wp-image-2172" title="mpath-hit19" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit19.png" width="485" height="240" /></a>

Two connections may show up from the same network interface with one of them showing “No” in the Managed column. This problem will resolve itself within about five minutes.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit20.png"><img class="alignnone size-full wp-image-2173" title="mpath-hit20" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit20.png" width="485" height="260" /></a>

You can see in the image below, both connections are now connected (two different network cards in the server to two different network interfaces on the SAN):
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit21.png"><img class="alignnone size-full wp-image-2174" title="mpath-hit21" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mpath-hit21.png" width="485" height="240" /></a>

You now have true multipath between your server and SAN. Maintenance that requires a single path to be unavailable can now be done without interruption to the server's connection to the SAN.

µ