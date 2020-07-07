---
ID: 1273
post_title: >
  Unable to connect to a Castelle FaxPress
  5000 – Solid Red Alarm
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/09/16/unable-to-connect-to-a-castelle-faxpress-5000-solid-red-alarm/
published: true
post_date: 2010-09-16 05:30:26
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/fp5000.jpg"><img class="alignleft size-thumbnail wp-image-1291" title="fp5000" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/09/fp5000.jpg?w=150" width="150" height="70" /></a>You are no longer able to communicate with your Castelle FaxPress 5000 device. The red light is on solid and the green light is off. Whether this is a new installation, you've moved the back-end portion of the software to a new server, or nothing has changed that you're aware or, here is a major pitfall that you may want to look into before scrapping the device and looking for an alternative solution.

The error shown below is what the clients who use "Faxmain" will receive. It's fairly generic and can be caused by several issues:

<em> "Unable to login to FaxPress (Network communication error (Client timeout). FaxPress client is unable to communicate with the server. If this problem persists, please notify your system administrator.) An irrecoverable error has occurred ( Reconnect to restore connection with the server ) CPI Error No. : -2221 CPI Error: Network communication error (Client timeout). FaxPress client is unable to communicate with the server. If the problem persists, please notify your system administrator. File: .\Source\LoginDlg.cpp Line No. :147</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/fp1.jpg"><img class="alignnone size-medium wp-image-1286" title="fp1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/09/fp1.jpg?w=300" width="300" height="230" />
</a>

If you’re receiving this error, the red light is on solid, and the green light is off, there is a good chance that the local user id is locked out that is created during the back-end installation of your FaxPress server. This user id is named the serial number of your FaxPress (example: 08222123). If the FaxPress user account is continuously locked out after verifying the password is the same for this user account and on the actual FaxPress device by using the “FP Configurator for TCP/IP”, the issue is that the “Do not Store LAN Manager hash value on next password change” is enabled. FaxPress requires the use of the LAN Manager hash to authenticate. For security purposes, you can disable this option; run gpupdate on the local computer if changing this setting at the domain level, set the FaxPress user password, and then re-enable this setting since it is a security risk to store this value and not something that you would want to do for all of your user accounts.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/09/fp2.jpg"><img class="alignnone size-medium wp-image-1287" title="fp2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/09/fp2.jpg?w=300" width="300" height="32" /></a>

Cycle the power on the FaxPress device and once you have a solid green light, you should be able to successfully connect to it with Faxmain.

µ