---
ID: 3014
post_title: >
  Unable to Print Incoming Faxes on
  FaxPress
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/11/03/unable-to-print-incoming-faxes-on-faxpress/
published: true
post_date: 2011-11-03 07:30:39
---
I recently received a call from a customer who has an older Castelle FaxPress 5000. They reported that their incoming faxes were no longer printing automatically even though they were receiving faxes in the "Unaddressed Faxes" queue in Faxmain.

To check the settings for automatically printing incoming faxes, open Faxmain, expand the tree, right click on the “Unaddressed Faxes” folder and select properties:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print0.png"><img class="alignnone size-full wp-image-3015" title="faxpress-print0" src="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print0.png" alt="" width="230" height="37" /></a>

Verify the “Print Incoming Fax” checkbox is checked on the “Incoming Faxes” tab:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print1.png"><img class="alignnone size-full wp-image-3016" title="faxpress-print1" src="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print1.png" alt="" width="383" height="491" /></a>

Verify the settings on the “Printer Configuration” tab and then click the “Setup” button:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print2.png"><img class="alignnone size-full wp-image-3017" title="faxpress-print2" src="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print2.png" alt="" width="383" height="491" /></a>

Select the ellipsis (…) and browse for the print server. Much of the necessary information will be filled in automatically and eliminate the potential for error.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print3.png"><img class="alignnone size-full wp-image-3018" title="faxpress-print3" src="http://mikefrobbins.com/wp-content/uploads/2011/11/faxpress-print3.png" alt="" width="317" height="231" />
</a>Host Name = Netbios name of the print server.
Printer Sharable Name = Share name of the printer.
HOST IP Address = IP Address of the print server.
Login Name = A username in the domain that has access to the shared printer.
Login Password = The password of the username specified in the above setting. This password <strong>MUST</strong> have a <a href="http://en.wikipedia.org/wiki/LM_hash" target="_blank">LAN Manager Hash</a>.

The problem that my customer experienced was that the specified account had it’s password changed or the account was changed to a new one that didn't have a LAN Manager Hash. This caused the specified account to become locked out after a certain number of times of attempting to print. Reversing the setting in my “<a href="http://mikefrobbins.com/2009/09/14/lan-manager-hash/" target="_blank">Removing the LAN Manager Hash security risk</a>” blog long enough to generate a new password with a LAN Manager Hash for this one account resolved the problem.

µ