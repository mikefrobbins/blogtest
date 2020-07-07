---
ID: 1542
post_title: >
  Microsoft Windows Server Update Services
  3.0 SP2 (WSUS)
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/12/23/microsoft-windows-server-update-services-3-0-sp2-wsus/
published: true
post_date: 2010-12-23 05:30:32
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/wsus.jpg"><img class="alignleft size-thumbnail wp-image-1545" title="wsus" src="http://mikefrobbins.com/wp-content/uploads/2010/12/wsus.jpg?w=150" alt="" width="150" height="117" /></a>I typically work as a systems engineer in the SMB sector and WSUS is one of the products that in my opinion is a no brainer to implement. It’s free with Windows Server 2003 or 2008 (and Vista &amp; Windows 7 I’m told, although I have not verified this). It can easily be implemented by your present IT staff and can reside on an existing IIS webserver server as long as there is enough local disk space to hold the updates. The updates cannot reside on a mapped drive or on a drive that’s specified via a UNC path, they must be stored locally if you choose to download them and have a centralized distribution point for the updates. If you do not have enough disk space on a local disk, you can still use WSUS to approve or decline updates in one centralized console, but have the clients download the updates directly from Microsoft.  I would only choose this option if absolutely necessary since it defeats part of the purpose for using WSUS. I typically use a tiered type install where the front-end is on an IIS webserver and the back-end is on an existing SQL server instead of allowing it to install SQL Express on my webserver.

I typically setup WSUS up so that it only downloads updates once they are approved, only for the necessary languages, and only for certain products and classifications since there’s no need to see the updates for something like Biztalk if there are no Biztalk servers in your environment. One of the biggest features that is missing is the ability to be able to select updates globally for specific hardware architectures. Most companies or at least the ones I have worked for do not have Itanium based servers so I do not want to approve those updates since they would consume unnecessary bandwidth to download them and disk space to store them. This creates a tedious task of going through and selecting all of the non-Itanium updates or selecting everything and then de-selecting the Itanium updates:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/wsus-itanium.png"><img class="alignnone size-full wp-image-1543" title="wsus-itanium" src="http://mikefrobbins.com/wp-content/uploads/2010/12/wsus-itanium.png" alt="" width="640" height="313" /></a>

Any manual process such as this is error prone and while it’s not the end of the world if one is accidentally selected, it would be much easier if Microsoft added a selection for architecture so I could deselect Itanium updates at a global level. This issue also prevents me from using the Automatic Approval features since it would automatically approve updates for Itanium hardware as well as the ones I wanted it to.

µ