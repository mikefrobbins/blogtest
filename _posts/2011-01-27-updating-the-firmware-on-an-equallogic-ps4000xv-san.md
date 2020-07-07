---
ID: 1668
post_title: >
  Updating the Firmware on an EqualLogic
  PS4000XV SAN
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/01/27/updating-the-firmware-on-an-equallogic-ps4000xv-san/
published: true
post_date: 2011-01-27 05:30:08
---
This blog will walk you through the steps of updating the firmware on an EqualLogic PS4000XV SAN (Storage Area Network) from version 4.3.0 to version 5.0.2.

Plan the Update
This process requires that the SAN be restarted so I recommend scheduling it outside of production hours or as scheduled downtime if you work in a 24/7 environment. I also recommend gracefully shutting down any servers that have data on the SAN since you would otherwise be ripping the drives out from underneath those servers.

Login to your <a href="http://support.dell.com/equallogic" target="_blank">EqualLogic support account</a> or request a support account from the login page if you don’t already have one. Download the documentation for the firmware update from the support site. Read the documentation! Do not continue until you have at least read the “Release Notes” and the “Updating Storage Array Firmware” documents. Depending on what version of firmware you’re currently running, you may have to upgrade to an interim release before upgrading to the latest version. To determine what version of firmware your SAN is running, open a web browser and log into the management interface as you normally would. Click on the member and then select the controllers tab. The firmware version is listed for each controller as shown in the image below:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu1.png"><img class="alignnone size-full wp-image-1669" title="sfu1" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu1.png" alt="" width="640" height="205" /></a>

According to Table 1 on page 4 of the “Upgrading Storage Array Firmware” document, direct upgrades to v5.0.2 are supported from the version of firmware that our SAN is running (v4.3.0). Download the actual firmware from the support site. It is very important to have the management interface on both controllers connected to a network connection that is accessible by the machine you are performing the upgrade from.

Perform the upgrade
Open a web browser and log into the management interface of your storage are network. Click on “Group Configuration” and then click on “Update firmware on group members”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu2.png"><img class="alignnone size-full wp-image-1670" title="sfu2" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu2.png" alt="" width="640" height="244" /></a>

Enter the administrative password and click OK:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu3.png"><img class="alignnone size-full wp-image-1671" title="sfu3" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu3.png" alt="" width="343" height="166" /></a>

Browse to the location where the firmware update file was download to and click open:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu4.png"><img class="alignnone size-medium wp-image-1672" title="sfu4" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu4.png?w=300" alt="" width="300" height="218" /></a>

Click Upgrade:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu5.png"><img class="alignnone size-full wp-image-1673" title="sfu5" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu5.png" alt="" width="640" height="231" /></a>

You should see the progress bar moving:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu6.png"><img class="alignnone size-full wp-image-1674" title="sfu6" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu6.png" alt="" width="640" height="231" /></a>

The status should automatically move to step 2 and continue without any intervention:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu7.png"><img class="alignnone size-full wp-image-1675" title="sfu7" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu7.png" alt="" width="640" height="231" /></a>

This portion of the upgrade is complete and a restart of the SAN is required. Click Restart:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu8.png"><img class="alignnone size-full wp-image-1676" title="sfu8" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu8.png" alt="" width="640" height="231" /></a>

Click Yes:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu9.png"><img class="alignnone size-full wp-image-1677" title="sfu9" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu9.png" alt="" width="315" height="113" /></a>

Wait while the SAN is "Preparing to restart":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu10.png"><img class="alignnone size-full wp-image-1678" title="sfu10" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu10.png" alt="" width="640" height="231" /></a>

Click Close:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu11.png"><img class="alignnone size-full wp-image-1679" title="sfu11" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu11.png" alt="" width="640" height="231" /></a>

You’ll notice the management interface has lost its connection to the SAN:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu12.png"><img class="alignnone size-full wp-image-1680" title="sfu12" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu12.png" alt="" width="302" height="43" /></a>

Not having both management network interfaces connected, could cause this error:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu13.png"><img class="alignnone size-full wp-image-1681" title="sfu13" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu13.png" alt="" width="151" height="113" /></a>

You can also run into an issue where both controllers aren’t updated if you don’t have the management interface on each controller connected to an accessible network connection. Having the firmware version out of sync on the controllers will generate a warning:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu14.png"><img class="alignnone size-medium wp-image-1682" title="sfu14" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu14.png?w=300" alt="" width="300" height="80" /></a>

Close the web browser and re-open it otherwise you’ll receive the following error:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu15.png"><img class="alignnone size-full wp-image-1683" title="sfu15" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu15.png" alt="" width="452" height="117" /></a>

If you happen to run the update again, you’ll notice it says “Member is up to date”, but you can reinstall the 5.0.2 firmware again if you need to for some reason. You cannot reinstall a previous version of firmware or downgrade without resetting the SAN to the factory defaults and losing all of your data.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu16.png"><img class="alignnone size-full wp-image-1684" title="sfu16" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu16.png" alt="" width="640" height="248" /></a>

Reapplying the update will give you different actions when it completes:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu17.png"><img class="alignnone size-full wp-image-1685" title="sfu17" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu17.png" alt="" width="640" height="248" /></a>

Once the update is complete, you’ll notice the management interface looks different:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu18.png"><img class="alignnone size-medium wp-image-1686" title="sfu18" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu18.png?w=300" alt="" width="300" height="160" /></a>

Verify the firmware on both controllers has been updated and are at the same version:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu19.png"><img class="alignnone size-full wp-image-1719" title="sfu19" src="http://mikefrobbins.com/wp-content/uploads/2011/01/sfu19.png" alt="" width="640" height="201" /></a>

Now all you need to do is boot up any servers that were shutdown before you began the process of updating the firmware. Verify these servers are able to access the SAN.

I have multiple clients who are running these storage area networks so I've done this particular firmware update a few times. As you can see in some of the screenshots, if you do not read and follow the instructions, it's very possible that you will experience issues.

<span style="text-decoration:underline;">April 12, 2011:</span>
Updating the firmware from v5.0.2 to v5.0.4 also requires the SAN to be restarted:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/san502-504.png"><img class="alignnone size-full wp-image-2231" title="san502-504" src="http://mikefrobbins.com/wp-content/uploads/2011/04/san502-504.png" alt="" width="640" height="240" /></a>

<span style="text-decoration:underline;">July 19, 2011:</span>
If you're updating the firmware on your EqualLogic SAN, be prepared to restart it since updating from v4.3.0 to v5.0.5, v5.0.2 to v5.0.5, or v5.0.4 to v5.0.5 requires a restart:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/san502-505.png"><img class="alignnone size-full wp-image-2611" title="san502-505" src="http://mikefrobbins.com/wp-content/uploads/2011/07/san502-505.png" alt="" width="640" height="241" /></a>

µ